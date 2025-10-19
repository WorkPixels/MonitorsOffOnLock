# MonitorsOffOnLock
This script will force your monitors to go into standby when you lock your PC.

### Prerequisites

1.  **Script Location:** Your working PowerShell script is saved at: `C:\Users\WorkPixels\OneDrive\Documents\Scripts\MonitorOffOnLock.ps1`.
2.  **User Context:** You will configure the task to run as your user account to guarantee the lock event is detected.

-----
# Script
Save the script to this location or update the location in your task.
"C:\Users\WorkPixels\OneDrive\Documents\Scripts\MonitorOffOnLock.ps1"

```
# ==============================================================================
# Monitor-Off Script (Used with Workstation Lock Trigger)
# ==============================================================================

Add-Type -TypeDefinition @'
using System;
using System.Runtime.InteropServices;
public class Monitor {
    [DllImport("user32.dll")]
    public static extern int SendMessage(int hWnd, int hMsg, int wParam, int lParam);
}
'@
[Monitor]::SendMessage(-1, 0x0112, 0xF170, 2)

# Forces the PowerShell process to terminate immediately, eliminating the window flash.
exit
```
-----

## Step-by-Step Task Scheduler Setup

### 1. Create the Task

1.  Open the **Task Scheduler**. Press Windows Key + R, type `taskschd.msc`, and press Enter.
2.  In the right-hand **Actions** panel, click **Create Task...**.

-----

### 2. Configure the General Tab

This defines the security context and name.

1. Name: Monitor Off on Lock
2. User account: Ensure this is set to your username (`WorkPixels`)
3. Security options: Select "Run only when user is logged on"
4. Check: "Run with highest privileges"
5. Configure for: Set this to **Windows 10/11** (or your current OS)

<img width="632" height="480" alt="image" src="https://github.com/user-attachments/assets/2e97ebe3-c95e-4c14-829c-948d553c1412" />

-----

### 3. Configure the Trigger Tab

This is the key step to ensure the script runs exactly when the PC is locked.

1.  Go to the **Triggers** tab and click **New...**.
2.  From the **"Begin the task"** dropdown, select **"On workstation lock"**.
3.  Ensure **"Any user"** is selected.
4.  Click **OK**.

<img width="632" height="480" alt="image" src="https://github.com/user-attachments/assets/7473400b-bc65-4668-af3b-1e5807e46679" />

-----

### 4. Configure the Action Tab

This tells the task to execute your PowerShell script.

1.  Go to the **Actions** tab and click **New...**.

2.  **Action:** Select **"Start a program"**.

3.  **Program/script:** Type **`powershell.exe`**

<img width="454" height="500" alt="image" src="https://github.com/user-attachments/assets/5ae593c3-bb17-4e32-a641-cc610a988511" />

4.  **Add arguments:** Paste the following, which ensures your script runs quickly and bypasses the execution policy:

    ```
    -WindowStyle Hidden -NoProfile -ExecutionPolicy Bypass -File "C:\Users\WorkPixels\OneDrive\Documents\Scripts\MonitorOffOnLock.ps1"
    ```

5.  Click **OK**.

<img width="632" height="480" alt="image" src="https://github.com/user-attachments/assets/65dcf7ad-26e5-4f1e-9f28-0a3e3aef4c28" />

-----

### 5. Configure the Settings Tab

This ensures the task is not stopped prematurely.

1.  Go to the **Settings** tab.
2.  Make sure **"Allow task to be run on demand"** is checked.
3.  **Uncheck** the box next to **"Stop the task if it runs longer than:"** (Since the script contains an `exit` command, this is less critical, but ensures compatibility).
4.  Click **OK** to finish. You will be prompted to enter your Windows password to confirm the task credentials.

<img width="632" height="480" alt="image" src="https://github.com/user-attachments/assets/1823c11e-14ea-43c7-93d5-2dcb16009645" />

## Testing the Setup

The task is now deployed. Test it by:

1.  Ensure you are logged into your PC.
2.  Pressing the **Windows Key + L** to lock your workstation.

Your monitors should turn off instantly and silently, without a visible PowerShell window.
