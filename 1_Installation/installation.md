- **Command for mounting a shared directory:**

  ```bash
  sudo mount -t virtiofs [shared_directory] [/path/to/share]
  ```

  **Usage:** This command is used to mount a shared directory between the host and the guest system. Replace `shared_directory` with the name of the shared directory and `/path/to/share` with the desired mount point on your system.
  **When to use:** Use this command when you need to access files from a shared directory on your Linux system.

- **Command for editing static file system info:**

  ```bash
  sudo nano /etc/fstab
  ```

  **Usage:** This command opens the `/etc/fstab` file in the `nano` text editor. The `/etc/fstab` file contains static information about the file systems and their mount points.
  **When to use:** Use this command to configure file systems to be mounted automatically at boot time.

- **Example entry for `/etc/fstab`:**

  ```
  [shared_directory] /mnt/shared-with-mac virtiofs rw,nofail 0 0
  ```

  **Explanation:** This line configures the `[shared_directory]` to be mounted at `/mnt/shared-with-mac` using the `virtiofs` file system. The options `rw,nofail` ensure the file system is mounted in read-write mode and does not cause boot failure if the mount fails.

- **Command to restart the machine:**
  ```bash
  sudo reboot
  ```
  **Usage:** This command restarts the Linux machine.
  **When to use:** Use this command after making changes to the `/etc/fstab` file or other system configurations that require a reboot to take effect.
