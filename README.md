# `[DllImport("name.dll", ...)]`
*Indicates that the attributed method is exposed by an unmanaged dynamic-link library (DLL) as a static entry point*

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
