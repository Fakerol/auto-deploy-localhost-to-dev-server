# CI/CD Automation for Your Project

This guide provides steps to set up and automate CI/CD for a project, enabling seamless deployment from a Windows localhost to a Linux server. The project is assumed to be hosted in a private GitHub repository and deployed on a Linux server at `/usr/share/nginx/html`.

## Prerequisites
- Project is deployed on a Linux server at `/usr/share/nginx/html`.
- Project is connected to a private GitHub repository.
- Git is installed on your Windows machine and the Linux server.
- SSH access to the Linux server is available.
- PowerShell is available on your Windows machine.

## Current Manual Workflow
1. **On Windows Localhost**:
   - Navigate to your project directory: `C:\Users\john_doe\Desktop\my-project`
   - Create a new branch: `git checkout -b feature/my-feature`
   - Develop and test your code.
   - Stage changes: `git add .`
   - Commit changes: `git commit -m "complete - my feature implementation"`
   - (Optional) Push branch to GitHub: `git push origin feature/my-feature`
   - Switch to main branch: `git checkout main`
   - Pull latest changes: `git pull origin main`
   - Merge feature branch: `git merge feature/my-feature`
   - Push to main: `git push origin main`
2. **On Primary Server**:
   - Open PowerShell and connect to the server: `ssh root@192.168.1.100`
   - Enter the server password: `<your-server-password>`
   - Navigate to project directory: `cd /usr/share/nginx/html`
   - Pull latest changes: `git pull origin main`

## Automated CI/CD Workflow
The automated workflow replaces manual server steps with a PowerShell script (`deploy.ps1`) that updates the server after pushing changes to the main branch.

### Steps to Set Up Automation
1. **Open Project on Windows**:
   - Navigate to your project directory: `C:\Users\john_doe\Desktop\my-project`

2. **Generate SSH Key**:
   - Open a terminal in the project directory.
   - Run: `ssh-keygen`
     - Press Enter to accept the default file location (`C:\Users\john_doe\.ssh\id_ed25519`).
     - Optionally set a passphrase (or leave blank for no passphrase).

3. **Copy Public Key**:
   - Open the public key file: `notepad C:\Users\john_doe\.ssh\id_ed25519.pub`
   - Copy the entire key (e.g., `ssh-ed25519 AAAAC3... user@hostname`).

4. **Set Up SSH on the Server**:
   - Log in to the server with password (one-time setup): `ssh root@192.168.1.100`
   - Enter password: `<your-server-password>`
   - Create SSH directory: `mkdir -p ~/.ssh && chmod 700 ~/.ssh`
   - Open (or create) `authorized_keys`: `nano ~/.ssh/authorized_keys`
   - Paste the copied public key (ensure it’s on one line).
   - Set permissions: `chmod 600 ~/.ssh/authorized_keys`
   - Exit the server: `exit`

5. **Test Passwordless Login**:
   - From Windows, run: `ssh root@192.168.1.100`
   - Verify that you log in without needing a password.

6. **Create Deployment Script**:
   - In `C:\Users\john_doe\Desktop\my-project`, create a file named `deploy.ps1` with the following content:

     ```powershell
     ssh root@192.168.1.100 "cd /usr/share/nginx/html && git pull origin main"
     ```

7. **Update Workflow**:
   - Follow the same local development steps as in the manual workflow:
     - Create branch: `git checkout -b feature/my-feature`
     - Develop and test code.
     - Stage: `git add .`
     - Commit: `git commit -m "complete - my feature implementation"`
     - (Optional) Push branch: `git push origin feature/my-feature`
     - Switch to main: `git checkout main`
     - Pull latest: `git pull origin main`
     - Merge: `git merge feature/my-feature`
     - Push to main: `git push origin main`
   - Deploy to server: Run `./deploy.ps1` in PowerShell.

## Running the Deployment
- Ensure you’re in the project directory: `C:\Users\john_doe\Desktop\my-project`
- Execute the script: `./deploy.ps1`
- The script will SSH into the server, navigate to `/usr/share/nginx/html`, and pull the latest changes from the `main` branch.

## Notes
- Replace `john_doe` with your actual Windows username.
- Replace `192.168.1.100` with your actual server IP address.
- Replace `<your-server-password>` with your actual server password (used only during initial setup).
- Ensure the Linux server has Git installed and is configured to access the private GitHub repository (e.g., via SSH keys or HTTPS credentials).
- If you encounter permission issues with `deploy.ps1`, run PowerShell as Administrator and set the execution policy: `Set-ExecutionPolicy -Scope CurrentUser -ExecutionPolicy RemoteSigned`

## Troubleshooting
- **SSH Login Fails**: Verify the public key is correctly pasted in `~/.ssh/authorized_keys` on the server and permissions are set (`chmod 700 ~/.ssh` and `chmod 600 ~/.ssh/authorized_keys`).
- **Git Pull Fails**: Ensure the server has access to the GitHub repository (check SSH keys or Git credentials on the server).
- **Script Execution Fails**: Confirm PowerShell execution policy allows scripts (`Get-ExecutionPolicy` and set as needed).
