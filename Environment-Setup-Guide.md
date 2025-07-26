# Environment Setup Guide for C# Development

This guide will help you set up your development environment for C# programming across different operating systems.

## Prerequisites
- Basic familiarity with command line/terminal
- Internet connection for downloading tools
- Administrator/sudo privileges (for installation)

## Windows Setup

### Option 1: Visual Studio Code (Recommended for Beginners)

1. **Download Visual Studio Code**
   - Go to [https://code.visualstudio.com/](https://code.visualstudio.com/)
   - Download the Windows installer
   - Run the installer and follow the setup wizard

2. **Install .NET SDK**
   - Go to [https://dotnet.microsoft.com/download](https://dotnet.microsoft.com/download)
   - Download the .NET SDK (not just the runtime)
   - Run the installer and follow the setup wizard

3. **Install C# Extension**
   - Open Visual Studio Code
   - Go to Extensions (Ctrl+Shift+X)
   - Search for "C#" and install the official Microsoft C# extension

4. **Verify Installation**
   ```cmd
   dotnet --version
   ```

### Option 2: Visual Studio Community (Full IDE)

1. **Download Visual Studio Community**
   - Go to [https://visualstudio.microsoft.com/vs/community/](https://visualstudio.microsoft.com/vs/community/)
   - Download the installer
   - Run the installer and select ".NET desktop development" workload

2. **Install .NET SDK** (if not included)
   - Follow the same steps as Option 1

## macOS Setup

### Option 1: Visual Studio Code (Recommended)

1. **Install Homebrew** (if not already installed)
   ```bash
   /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
   ```

2. **Install .NET SDK via Homebrew**
   ```bash
   brew install dotnet
   ```

3. **Download Visual Studio Code**
   - Go to [https://code.visualstudio.com/](https://code.visualstudio.com/)
   - Download the macOS version
   - Drag to Applications folder

4. **Install C# Extension**
   - Open Visual Studio Code
   - Go to Extensions (Cmd+Shift+X)
   - Search for "C#" and install the official Microsoft C# extension

5. **Verify Installation**
   ```bash
   dotnet --version
   ```

### Option 2: Manual .NET Installation

1. **Download .NET SDK**
   - Go to [https://dotnet.microsoft.com/download](https://dotnet.microsoft.com/download)
   - Download the macOS .NET SDK installer
   - Run the installer

2. **Follow Visual Studio Code steps** from Option 1

## Debian/Ubuntu Setup

### Option 1: Using Package Manager (Recommended)

1. **Update package list**
   ```bash
   sudo apt update
   ```

2. **Install .NET SDK**
   ```bash
   sudo apt install dotnet-sdk-8.0
   ```

3. **Install Visual Studio Code**
   ```bash
   # Download the .deb package
   wget -qO- https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > packages.microsoft.gpg
   sudo install -o root -g root -m 644 packages.microsoft.gpg /etc/apt/trusted.gpg.d/
   sudo sh -c 'echo "deb [arch=amd64,arm64,armhf signed-by=/etc/apt/trusted.gpg.d/packages.microsoft.gpg] https://packages.microsoft.com/repos/code stable main" > /etc/apt/sources.list.d/vscode.list'
   sudo apt update
   sudo apt install code
   ```

4. **Install C# Extension**
   - Open Visual Studio Code
   - Go to Extensions (Ctrl+Shift+X)
   - Search for "C#" and install the official Microsoft C# extension

5. **Verify Installation**
   ```bash
   dotnet --version
   ```

### Option 2: Manual .NET Installation

1. **Download .NET SDK**
   ```bash
   wget https://download.microsoft.com/download/8/8/5/885e5d57-917c-4e25-b3be-4e3bdbeb030a/dotnet-sdk-8.0.100-linux-x64.tar.gz
   ```

2. **Extract and install**
   ```bash
   mkdir -p $HOME/dotnet
   tar zxf dotnet-sdk-8.0.100-linux-x64.tar.gz -C $HOME/dotnet
   ```

3. **Add to PATH**
   ```bash
   echo 'export DOTNET_ROOT=$HOME/dotnet' >> ~/.bashrc
   echo 'export PATH=$PATH:$HOME/dotnet' >> ~/.bashrc
   source ~/.bashrc
   ```

4. **Follow Visual Studio Code steps** from Option 1

## Arch Linux Setup

### Option 1: Using Pacman (Recommended)

1. **Install .NET SDK**
   ```bash
   sudo pacman -S dotnet-sdk
   ```

2. **Install Visual Studio Code**
   ```bash
   sudo pacman -S code
   ```

3. **Install C# Extension**
   - Open Visual Studio Code
   - Go to Extensions (Ctrl+Shift+X)
   - Search for "C#" and install the official Microsoft C# extension

4. **Verify Installation**
   ```bash
   dotnet --version
   ```

### Option 2: Using AUR (Alternative)

1. **Install yay** (if not already installed)
   ```bash
   sudo pacman -S --needed git base-devel
   git clone https://aur.archlinux.org/yay.git
   cd yay
   makepkg -si
   ```

2. **Install .NET SDK via AUR**
   ```bash
   yay -S dotnet-sdk-bin
   ```

3. **Follow Visual Studio Code steps** from Option 1

## Verification and Testing

### Test Your Installation

1. **Check .NET version**
   ```bash
   dotnet --version
   ```

2. **Create a test project**
   ```bash
   dotnet new console -n TestProject
   cd TestProject
   dotnet run
   ```

3. **Expected output**: "Hello, World!"

## Troubleshooting

### Common Issues

#### "dotnet command not found"
- **Windows**: Restart your terminal/command prompt
- **macOS/Linux**: Restart your terminal or run `source ~/.bashrc`

#### Permission Denied Errors
- **Linux/macOS**: Use `sudo` for installation commands
- **Windows**: Run as Administrator

#### Visual Studio Code Extensions Not Working
- Ensure you have the correct C# extension installed
- Restart Visual Studio Code after installation

#### .NET SDK Installation Fails
- Check your internet connection
- Try downloading the installer manually from Microsoft's website
- Ensure you have sufficient disk space (at least 1GB free)

### Platform-Specific Issues

#### Windows
- **Antivirus blocking installation**: Temporarily disable antivirus or add exceptions
- **Windows Defender**: Allow .NET SDK through Windows Defender

#### macOS
- **Gatekeeper blocking**: Right-click the installer and select "Open"
- **M1 Mac issues**: Ensure you're downloading the ARM64 version if available

#### Linux
- **Missing dependencies**: Install required packages:
  ```bash
  sudo apt install libc6-dev libgcc1 libgssapi-krb5-2 libicu-dev libssl-dev libstdc++6 zlib1g-dev
  ```
- **Arch Linux**: If pacman fails, try updating the system first:
  ```bash
  sudo pacman -Syu
  ```

## Alternative IDEs

### JetBrains Rider
- Professional C# IDE
- Available for all platforms
- Requires license (free for students)

### Visual Studio for Mac
- Full-featured IDE for macOS
- Free Community edition available

### MonoDevelop
- Open-source alternative
- Available for Linux and macOS

## Next Steps

Once your environment is set up:

1. **Create your first project** following Project 1: Hello World
2. **Explore the IDE** - learn keyboard shortcuts and features
3. **Set up Git** for version control
4. **Join the community** - Stack Overflow, Reddit r/csharp, Discord servers

## Resources

- [Official .NET Documentation](https://docs.microsoft.com/en-us/dotnet/)
- [Visual Studio Code Documentation](https://code.visualstudio.com/docs)
- [C# Language Reference](https://docs.microsoft.com/en-us/dotnet/csharp/)
- [.NET Download Page](https://dotnet.microsoft.com/download)

## Support

If you encounter issues not covered in this guide:

1. Check the official documentation
2. Search Stack Overflow
3. Ask in programming communities
4. Check GitHub issues for the specific tools you're using

---

**Note**: This guide covers the most common setup scenarios. For enterprise environments or specific requirements, consult your organization's IT department or the official Microsoft documentation. 