---
layout: "../../layouts/BlogPost.astro"
title: "Upgrade all windows packages with one command"
description: "Winget upgrade all accept source"
pubDate: "March 31 2024"
# heroImage: "/placeholder-hero.jpg"
---

In a Command Prompt or PowerShell instance **opened as administrator**, enter

```shell
winget upgrade --all --accept-source-agreements --accept-package-agreements
```

## An explanation of how I arrived at this procedure

According to the [winget documentation](https://learn.microsoft.com/en-us/windows/package-manager/winget/upgrade), the command and single flag to upgrade all packages on Windows is

```shell
winget upgrade --all
```

When running this command, Winget will sometimes still prompt the user to enter `y/n` into the command line to accept license or source agreements of certain packages. However, we can easily silence these prompts by adding some flags. According to [a GitHub forum user's comment](https://github.com/microsoft/winget-cli/issues/2680#issuecomment-1937804901), winget automatically accepts license or source agreements if we add two flags, `--accept-source-agreements` and `--accept-package-agreements`.

```shell
winget upgrade --all --accept-source-agreements --accept-package-agreements
```

While the above command automatically accepts all prompts that Winget would've displayed inside the command line, Winget will still push out prompts requesting administrator privileges to the Windows GUI, known as "elevation prompts". To automatically accept these, we need to initially run Winget with administrator privileges.

## Grant Admin Priviliges to `winget` 

Open up a system command line interpreter as an administrator. Windows by default has two command line interpreters. These are PowerShell and Command Prompt. 

There are a ton of ways to open up Powershell and Command Prompt with administrator privileges. But, the most aesthetically pleasing way is to open up the Windows terminal as an administrator, then open up Powershell in a new tab, which will automatically inherit administrator privileges from the terminal. 

Press the Windows key and type `terminal`. Don't click on it, instead, when the best match option highlights the terminal, hold `ctrl+shift+enter`, in that order. You should receive an elevation prompt requesting administrator privileges. Click `yes` for this prompt. Then, the terminal should automatically open, and by default, the terminal should open up a new PowerShell tab. 

Input the above command to upgrade all packages.

## Won't running Winget as administrator mess up my file permissions?

File permissions in Windows function differently than in Linux. In Linux, creating files as the root user with `sudo` will create files only modifiable by the root user. 

In Windows,

- Created files are [assigned an owner](https://learn.microsoft.com/en-us/previous-versions/windows/it-pro/windows-server-2003/cc783530(v=ws.10)#ownership), which by default is the user that creates the file. The owner of a file can always modify it, no matter what.
    - You can test this by creating a new standard user in Windows and creating a file from an elevated Powershell instance. The owner of the file will still be the new user.
- When a user attempts to modify a file, the system checks the user's [access token](https://learn.microsoft.com/en-us/windows/win32/secauthz/how-dacls-control-access-to-an-object#dacls) to determine if they are allowed to modify a file or not.
- "Elevation" is the act of an administrator giving the user an [administrator access token](https://learn.microsoft.com/en-us/windows/security/application-security/application-control/user-account-control/how-it-works#sign-in-process).

Therefore, even though a user may have elevated privileges through their access token when creating a file, the files they create *will still be owned by them*. In other words, files created with elevated privileges are still owned by the user (unless the file is created in a system folder or folder owned by an administrator, in which case the regular user shouldn't be able to read/write into them without administrator privileges anyway), so there's no difference between a file created by a user and a user with an administrator access token.

## Won't running Winget as administrator give installers that do not require administrator permissions to install administrator permissions?

According to the `winget-cli` repository [readme](https://github.com/microsoft/winget-cli?tab=readme-ov-file#administrator-considerations), 
> some applications may require elevation to install 
>
> [...] 
>
> When running winget in an Administrator Command Prompt, you will not see elevation prompts if the application requires it.

This means that administrator privileges will only be granted to applications that prompt the user for administrator privileges.

## Caveats

`rustup` currently opens up a new window and will stall at a certain point in installation until some input is provided into the standard input, but most other packages install without any user input.