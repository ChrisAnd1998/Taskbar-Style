# Taskbar-Style
This code allows you to set the Windows taskbar Style to Transparant, Blur or Acrylic in VB.NET/C# like TranslucentTB does.



<details><summary>VB.NET</summary>

Declarations
```vb.net
    Friend Structure WindowCompositionAttributeData
        Public Attribute As WindowCompositionAttribute
        Public Data As IntPtr
        Public SizeOfData As Integer
    End Structure

    Friend Enum WindowCompositionAttribute
        WCA_ACCENT_POLICY = 19
    End Enum

    Friend Enum AccentState
        ACCENT_DISABLED = 0
        ACCENT_ENABLE_GRADIENT = 1
        ACCENT_ENABLE_TRANSPARENTGRADIENT = 2
        ACCENT_ENABLE_BLURBEHIND = 3
        ACCENT_ENABLE_TRANSPARENT = 6
        ACCENT_ENABLE_ACRYLICBLURBEHIND = 4
    End Enum

    <StructLayout(LayoutKind.Sequential)>
    Friend Structure AccentPolicy
        Public AccentState As AccentState
        Public AccentFlags As Integer
        Public GradientColor As Integer
        Public AnimationId As Integer
    End Structure

    Friend Declare Function SetWindowCompositionAttribute Lib "user32.dll" (ByVal hwnd As IntPtr, ByRef data As WindowCompositionAttributeData) As Integer
    Private Declare Auto Function FindWindow Lib "user32.dll" (ByVal lpClassName As String, ByVal lpWindowName As String) As IntPtr
```

Code
```vb.net
    Friend Sub EnableTaskbarStyle()
        Dim tskBarClassName As String = "Shell_TrayWnd"
        Dim tskBarHwnd As IntPtr = FindWindow(tskBarClassName, Nothing)
        Dim accent = New AccentPolicy()
        Dim accentStructSize = Marshal.SizeOf(accent)

        ' # Taskbar Style Acrylic
        ' accent.AccentState = AccentState.ACCENT_ENABLE_ACRYLICBLURBEHIND
        ' accent.GradientColor = 10 'Or 16777215

        ' # Taskbar Style Blur
        ' accent.AccentState = AccentState.ACCENT_ENABLE_BLURBEHIND

        ' # Taskbar Style Transparent
        accent.AccentState = AccentState.ACCENT_ENABLE_TRANSPARENT

        Dim accentPtr = Marshal.AllocHGlobal(accentStructSize)
        Marshal.StructureToPtr(accent, accentPtr, False)
        Dim data = New WindowCompositionAttributeData()
        data.Attribute = WindowCompositionAttribute.WCA_ACCENT_POLICY
        data.SizeOfData = accentStructSize
        data.Data = accentPtr
        SetWindowCompositionAttribute(tskBarHwnd, data)
        Marshal.FreeHGlobal(accentPtr)
    End Sub
```
</details>

<details><summary>C#</summary>

Declarations
```cs
using System;
using System.Runtime.InteropServices;

// ...

public class Win32
{
    public struct WindowCompositionAttributeData
    {
        public WindowCompositionAttribute Attribute;
        public IntPtr Data;
        public int SizeOfData;
    }

    public enum WindowCompositionAttribute
    {
        WCA_ACCENT_POLICY = 19
    }

    public enum AccentState
    {
        ACCENT_DISABLED = 0,
        ACCENT_ENABLE_GRADIENT = 1,
        ACCENT_ENABLE_TRANSPARENTGRADIENT = 2,
        ACCENT_ENABLE_BLURBEHIND = 3,
        ACCENT_ENABLE_TRANSPARENT = 6,
        ACCENT_ENABLE_ACRYLICBLURBEHIND = 4
    }

    [StructLayout(LayoutKind.Sequential)]
    public struct AccentPolicy
    {
        public AccentState AccentState;
        public int AccentFlags;
        public int GradientColor;
        public int AnimationId;
    }

    [DllImport("user32.dll")]
    public static extern int SetWindowCompositionAttribute(IntPtr hwnd, ref WindowCompositionAttributeData data);

    [DllImport("user32.dll", CharSet = CharSet.Auto)]
    public static extern IntPtr FindWindow(string lpClassName, string lpWindowName);
}
```

Code
```cs
using System;
using System.Runtime.InteropServices;

// ...

static void EnableTaskbarStyle()
{
    string tskBarClassName = "Shell_TrayWnd";
    IntPtr tskBarHwnd = Win32.FindWindow(tskBarClassName, default);
    var accent = new Win32.AccentPolicy();
    int accentStructSize = Marshal.SizeOf(accent);

    // Taskbar Style Acrylic
    // accent.AccentState = Win32.AccentState.ACCENT_ENABLE_ACRYLICBLURBEHIND;
    // accent.GradientColor = 10; // Or 16777215

    // Taskbar Style Blur
    // accent.AccentState = Win32.AccentState.ACCENT_ENABLE_BLURBEHIND;

    // Taskbar Style Transparent
    accent.AccentState = Win32.AccentState.ACCENT_ENABLE_TRANSPARENT;

    var accentPtr = Marshal.AllocHGlobal(accentStructSize);
    Marshal.StructureToPtr(accent, accentPtr, false);
    var data = new Win32.WindowCompositionAttributeData
    {
        Attribute = Win32.WindowCompositionAttribute.WCA_ACCENT_POLICY,
        SizeOfData = accentStructSize,
        Data = accentPtr
    };
    Win32.SetWindowCompositionAttribute(tskBarHwnd, ref data);
    Marshal.FreeHGlobal(accentPtr);
}
```
</details>
