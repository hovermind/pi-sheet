<a name="#toc"></a>TOC
 - [DllImport](#dllimport)
 - [MarshalAs](#marshalas)
 - [StructureLayout](#struct-layout)
 - [Marshaling Structure and Class](#marshaling-structure-and-class)
 - [Function Prototyping](#function-prototyping)
 - [Marshaling Delegate as Callback](#marshaling-delegate-as-callback)
 - [Misc](#misc)

<br><br>

## <a name="#dllimport"></a>DllImport

`[DllImport("name.dll", ...)]`

*Indicates that the attributed method is exposed by an unmanaged dynamic-link library (DLL) as a static entry point.*

| Field | Usage |
|-------|-------|
| EntryPoint | *Indicates the name or ordinal (i.e. #1) of the DLL entry point to be called*. **default is C# method name.**<br>`[DllImport("deno.dll", EntryPoint = "testPrint")]` |
| CallingConvention | *Indicates the calling convention of an entry point.* **default is __stdcall.**<br>`[DllImport("demo.dll", CallingConvention=CallingConvention.Cdecl)]` |
| CharSet | *To specify the marshaling behavior of string parameters & name mangling.* **default is CharSet.Ansi.**<br>`[DllImport("demo.dll", CharSet=CharSet.Unicode)]` |
| ExactSpelling | *to search an unmanaged DLL for entry-point names other than the one specified.* **default is false.**<br>`[DllImport("demo.dll", CharSet=CharSet.Unicode, ExactSpelling=true)]` |
| BestFitMapping | *mapping of managed Unicode characters to unmanaged ANSI characters.* **default is true.**<br>`[DllImport("demo.dll", CharSet=CharSet.Unicode, BestFitMapping=true)]` | 
| ThrowOnUnmappableChar | *throwing of an exception on an unmappable Unicode character that is converted to an ANSI "?" character..* **default is false.**<br>`[DllImport("demo.dll", BestFitMapping=true, ThrowOnUnmappableChar=false)]` |
| PreserveSig | *indicates whether unmanaged methods that have HRESULT or retval return values are directly translated or whether HRESULT or retval return values are automatically converted to exceptions..* **default is true.**<br>`[DllImport("demo.dll", ExactSpelling = true, PreserveSig = false)]` |
| SetLastError | *enables the caller to use the Marshal.GetLastWin32Error API function to determine whether an error occurred while executing the method.* **default is false.**<br>`[DllImport("demo.dll", SetLastError=true)]` |

Details: [DllImportAttribute Class](https://msdn.microsoft.com/en-us/library/system.runtime.interopservices.dllimportattribute)

<br><br>

## <a name="#marshalas"></a>MarshalAs

`[MarshalAs(UnmanagedType.x)]`

*Indicates how to marshal the data between managed and unmanaged code. You can apply this attribute to **parameters, fields, or return values**. This attribute is optional, as each data type has a default marshaling behavior. This attribute is only necessary when a given type can be marshaled to multiple types **i.e. String***

| Native | C# |
|--------|----|
|`void demo(char* data)` | pass-by-value<br>`static extern void demo([MarshalAs(UnmanagedType.LPStr)] string data)` |
| `void demo(char* data, int size)` | pass-by-ref : **IntPtr**<br>`static extern void demo(IntPtr buffer, int size)` |
| `void demo(char* data, int size)` | pass-by-ref : **StringBuilder**<br>`static extern void demo([MarshalAs(UnmanagedType.LPStr)] StringBuilder buffer, int size)` |
| `char x` | `[MarshalAs(UnmanagedType.U1)] public char x` |
| `char* x` | `[MarshalAs(UnmanagedType.LPStr)] public string x` |
| `char x[256]` | `[MarshalAs(UnmanagedType.ByValTStr, SizeConst=256)] public string x` |
| `WCHAR* x` | `[MarshalAs(UnmanagedType.LPWStr)] public string x` |
| `WCHAR x[256]` | `[MarshalAs(UnmanagedType.ByValTStr, SizeConst=256)] public string x` |
| `TCHAR* x` | `[MarshalAs(UnmanagedType.LPTStr)] public string x` |
| `TCHAR x[256]` | `[MarshalAs(UnmanagedType.ByValTStr, SizeConst=256)] public string x` |
| `bool` | `MarshalAs(UnmanagedType.U1)` / `MarshalAs(UnmanagedType.I1)` |
| `BOOL` | `MarshalAs(UnmanagedType.Bool)` |
| `int numbers[10]` | `[MarshalAs(UnmanagedType.ByValArray, SizeConst=10)] public int[] Numbers` |

Details: [MarshalAsAttribute Class](https://msdn.microsoft.com/en-us/library/system.runtime.interopservices.marshalasattribute) & [UnmanagedType Enumeration](https://msdn.microsoft.com/en-us/library/system.runtime.interopservices.unmanagedtype)

<br><br>

## <a name="struct-layout"></a>StructLayout

`[StructLayout(LayoutKind.x, Size=xx, CharSet=CharSet.x, Pack=x)]`

*Lets you control the physical layout of the data fields of a class or structure in memory. Applied to **Structure & Class***

| Field | Usage |
|-------|-------|
| LayoutKind | *Controls the layout of an object when exported to unmanaged code. **Default is Auto***<br>`[StructLayout(LayoutKind.Sequential)]` |
| CharSet | *Indicates whether string data fields within the class should be marshaled as **LPWSTR or LPSTR by default**.*<br>`[StructLayout(LayoutKind.Sequential, CharSet=CharSet.Ansi)]` |
| Size | *Indicates the absolute size of the class or structure. This field must be equal or greater than the total size (**in bytes**) of the members of the class or structure.*<br>`[StructLayout(LayoutKind.Sequential, Size=16)]` |
| Pack | *Controls the alignment of data fields of a class or structure in memory. **Default value is 0***<br>`[StructLayout(LayoutKind.Sequential, Pack=1)]` |

Details: [StructLayoutAttribute Class](https://docs.microsoft.com/en-us/dotnet/api/system.runtime.interopservices.structlayoutattribute)

<br><br>

## <a name="#marshaling-structure-and-class">Marshaling Structure and Class
 
*[MarshalAs](#marshalas) & [StructureLayout](#struct-layout) attributes are used to marshal Structure & Class*
 
**Native Code** 
```
struct MessageStruct
{
  char MsgID[11];
  char MsgText[501];
  char Status;
  int Numbers[10]
};

void modifyMessage(struct MessageStruct userMsg)
void modifyMessage(struct MessageStruct* userMsg)
```
**C# Code**
```
void modifyMessage(struct MessageStruct userMsg)
void modifyMessage(struct MessageStruct* userMsg)

[StructLayout(LayoutKind.Sequential, CharSet = CharSet.Ansi, Pack = 1)]
public unsafe struct MessageStruct
{
    [MarshalAs(UnmanagedType.ByValTStr, SizeConst = 11)]
    public string MsgID;

    [MarshalAs(UnmanagedType.ByValTStr, SizeConst = 501)]
    public string MsgText;

    [MarshalAs(UnmanagedType.U1)]
    public char Status;
    
    [MarshalAs(UnmanagedType.ByValArray, SizeConst=10)]
    public int[] Numbers;
}

void ModifyMessage(MessageStruct userMsg) // by value
void ModifyMessage(out MessageStruct userMsg) // by ref
void ModifyMessage(IntPtr userMsg) // by ref
```

#### FieldOffsetAttribute

*Used with LayoutKind.Explicit to indicates physical position of fields within unmanaged representation of a class or structure.*

```
[StructLayout(LayoutKind.Explicit, Size=14, CharSet=CharSet.Ansi)]
public struct Rect 
{
   [FieldOffset(0)] public int left;
   [FieldOffset(4)] public int top;
   [FieldOffset(8)] public int right;
   [FieldOffset(12)] public int bottom;
}

[StructLayout(LayoutKind.Explicit, Size=16, CharSet=CharSet.Ansi)]
public class MySystemTime 
{
   [FieldOffset(0)]public ushort wYear; 
   [FieldOffset(2)]public ushort wMonth;
   [FieldOffset(4)]public ushort wDayOfWeek; 
   [FieldOffset(6)]public ushort wDay; 
   [FieldOffset(8)]public ushort wHour; 
   [FieldOffset(10)]public ushort wMinute; 
   [FieldOffset(12)]public ushort wSecond; 
   [FieldOffset(14)]public ushort wMilliseconds; 
}
```
Details: [Marshaling Classes, Structures](https://docs.microsoft.com/en-us/dotnet/framework/interop/marshaling-different-types-of-arrays)

<br><br>

## <a name="#marshaling-delegate-as-callback">Marshaling Delegate as Callback

Delegate is attributed with [UnmanagedFunctionPointer](https://msdn.microsoft.com/en-us/library/system.runtime.interopservices.unmanagedfunctionpointerattribute) : `[UnmanagedFunctionPointer(CallingConvention.x)]`

* **NB-1 :** When you use a delegate inside a call, the common language runtime protects the delegate from being garbage collected for the duration of that call. However, if the unmanaged function stores the delegate to use after the call completes, you must manually prevent garbage collection until the unmanaged function finishes with the delegate.

* **NB-2 :** Can not use generic delegate i.e. `Action<T>`, `Func<T>`

**Native Code**
``` 
double mathOperation(int x, int y, double(*lambda)(int inputOne, int inputTwo));`
```
**C# Wrapper**
```
[UnmanagedFunctionPointer(CallingConvention.StdCall)]
public delegate double OperationHandler(int x, int y);

[DllImport("NativeLib.dll", EntryPoint = "mathOperation")]
private static extern double MathOperation(int x, int y, OperationHandler operationHandler);

// [MarshalAs(UnmanagedType.x)] should be applied for parameters if needed
```
**C# Caller**
```
var result = MathOperation(10, 5, (x, y) => {
    return x * y;
});
```
<br><br>

## <a name="#function-prototyping"></a>Function Prototyping

*Managed functions are attributed with [DllImport](#dllimport) & [MasrshalAs](#marshalas). Marshaling behavior is controlled through these attributes.*

| Native | C# |
|--------|----|
| `void demo(char flag)` | `[DllImport("demo.dll", EntryPoint="demo", CallingConvention=CallingConvention.Cdecl)]`<br>`static extern void Demo([MarshalAs(UnmanagedType.LPStr)] string data)` |
| `void demo(char* data)` | `[DllImport("demo.dll", EntryPoint="demo", CallingConvention=CallingConvention.Cdecl)]`<br>`static extern void Demo([MarshalAs(UnmanagedType.U1)] char flag)` |
| `void demo(char* data)` | `[DllImport("demo.dll", EntryPoint="demo", CallingConvention=CallingConvention.Cdecl)]`<br>`static extern void demo(IntPtr buffer)`<br><br>`[DllImport("demo.dll", EntryPoint="demo", CallingConvention=CallingConvention.Cdecl)]`<br>`static extern void demo([MarshalAs(UnmanagedType.LPStr)] StringBuilder buffer)` |
| CPP : `void demo(bool isOk)` | `[DllImport("demo.dll", EntryPoint="demo", CallingConvention=CallingConvention.Cdecl)]`<br>`static extern void Demo(MarshalAs(UnmanagedType.Bool) bool isOk)` |
| `void modifyStruct(struct MyStruct myStruct)` | `[DllImport("demo.dll", EntryPoint="modifyStruct", CallingConvention=CallingConvention.Cdecl)]`<br>`void ModifyStruct(MyStruct myStruct)` |
| `void modifyStruct(struct MyStruct* myStruct)` | `[DllImport("demo.dll", EntryPoint="modifyStruct", CallingConvention=CallingConvention.Cdecl)]`<br>`void ModifyStruct(out MyStruct myStruct)<br>void ModifyMessage(IntPtr myStruct)` |
| `double mathOperation(int x, int y, double(*lambda)(int xx, int yy));` | `[UnmanagedFunctionPointer(CallingConvention.StdCall)]<br>public delegate double OperationHandler(int x, int y);`<br><br>`[DllImport("NativeLib.dll", EntryPoint = "mathOperation")]<br>static public extern double MathOperation(int x, int y, OperationHandler oh)` |

#### Return Value
`[return: MarshalAs(UnmanagedType.x)] ` is used to override default marshaling vehavior of returned value.
```
[return: MarshalAs(UnmanagedType.LPTStr)] 
public string GetMessage()
{
    return "Hello Hooman";
}
```
#### [In, Out]
When to use:
 - native code requires pass-by-reference (a pointer) & the parameter type is a value type.
 - you expect to see any changes made by the native code to a struct or class member back in your code

**Native Code**
```
int testArrayOfInts(int* pArray, int pSize);

int testArrayOfStrings(char** ppStrArray, int size);

int testArrayOfStructs(MyPoint* pStructArray, int size); 
``` 

**C# Code**
```
[DllImport("demo.dll", EntryPoint="testArrayOfInts", CallingConvention=CallingConvention.Cdecl)]
public static extern int TestArrayOfInts([In, Out] int[] array, int size);

[DllImport("demo.dll", EntryPoint="testArrayOfStrings", CallingConvention=CallingConvention.Cdecl)]
public static extern int TestArrayOfStrings([In, Out] String[] stringArray, int size);

[DllImport("demo.dll", EntryPoint="testArrayOfStructs", CallingConvention=CallingConvention.Cdecl)]
public static extern int TestArrayOfStructs([In, Out] MyPoint[] pointArray, int size ); 
```
<br><br>

## <a name="misc"></a>Misc

Some Hacks & Tips: Pending ... ... ...



