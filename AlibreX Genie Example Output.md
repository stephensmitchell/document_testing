A console app that prints all PDM projects from all active safes:

```csharp
using System;
using System.Runtime.InteropServices;
using AlibreX;

class Program
{
    static IAutomationHook Hook;
    static IADRoot Root;

    static void Main()
    {
        try
        {
            Hook = (IAutomationHook)Marshal.GetActiveObject("AlibreX.AutomationHook");
            Root = (IADRoot)Hook.Root;

            IADPDMServerConnection conn = Root.GetActiveServerConnection();
            IADPDMSafes safes = conn.Safes;

            for (int s = 0; s < safes.Count; s++)
            {
                IADPDMSafe safe = safes.Item(s);

                Console.WriteLine($"Safe: {safe.Name}");

                IADPDMSafeProjects projects = safe.Projects;

                for (int p = 0; p < projects.Count; p++)
                {
                    IADPDMSafeProject project = projects.Item(p);
                    Console.WriteLine($"  {project.Name} ({project.Reference})");
                }

                Console.WriteLine();
            }
        }
        finally
        {
            if (Root != null) { Marshal.ReleaseComObject(Root); Root = null; }
            if (Hook != null) { Marshal.ReleaseComObject(Hook); Hook = null; }

            GC.Collect();
            GC.WaitForPendingFinalizers();
        }
    }
}
```

Assumption: an active PDM connection already exists in the Alibre UI (`GetActiveServerConnection()`). If not, connect first with `IADRoot.ConnectToPDM(...)`. `IADPDMSafe.Projects` exposes the projects collection, and each project is an `IADPDMSafeProject`.

**Verified from**

- `IAutomationHook.Root`: PASS
- `IADRoot.GetActiveServerConnection()`: PASS
- `IADPDMServerConnection.Safes`: PASS
- `IADPDMSafe.Projects`: PASS
- `IADPDMSafeProjects.Count`: PASS
- `IADPDMSafeProjects.Item(object)`: PASS
- `IADPDMSafeProject.Name`: PASS (inherited from `IADPDMFolder`)
- `IADPDMSafeProject.Reference`: PASS (inherited from `IADPDMFolder`)

Verified against the uploaded AlibreX indexes using `verify_alibrex.py`.




- Alibre Design
	- AlibreX Genie - Alibre Design v29, improved PDM understanding
		- https://chatgpt.com/g/g-6a18eb55ea7c8191b6726e4faa17a2cb-alibrex-genie
		- [[AlibreX Genie Example Output]]
	- AlibreX Genie 2 - Alibre Design v29, general assistant
		- https://chatgpt.com/g/g-6a18f18b89708191b1ae1286e1fac2ef-alibrex-genie-2
	- Alibre Script Genie - Alibre Design v28 Alibre Script
		- https://chatgpt.com/g/g-694c07c9dbf08191b263b16284050976-alibre-script-genie
	- Alibre Script Genie 2 - Alibre Design v29 Alibre Script
		- https://chatgpt.com/g/g-6a1a101b595c8191be149b7c39bdb150-alibre-script-genie-2

