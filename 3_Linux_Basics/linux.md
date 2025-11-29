# Linux Basics

## Superuser and Privileges

### Using sudo

Regular users cannot access the root directory, but with superuser privileges we can temporarily elevate our permissions.

```bash
sudo ls /root
```

### Administrator Access

Making a regular user an administrator = giving them access to the `sudo` command.

---

## Package Management with APT

### Updating Packages

**Fetch available updates:**

```bash
sudo apt update
```

**Upgrade packages:**

- `sudo apt upgrade` - Updates all packages **and their dependencies**
- `sudo apt-get upgrade` - Updates only packages (**not** their dependencies)

**Full system upgrade:**

```bash
sudo apt full-upgrade
# or
sudo apt dist-upgrade
```

This will upgrade existing packages **and** remove/install dependencies as needed.

### Installing and Removing Software

**Install software:**

```bash
sudo apt install [package_name]
```

**Remove software:**

```bash
sudo apt remove [package_name]
```
