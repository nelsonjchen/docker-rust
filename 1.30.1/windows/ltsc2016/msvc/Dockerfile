# escape=`

# IMAGES BUILT FROM THIS DOCKERFILE ARE NOT TO BE SHARED ON PUBLIC DOCKER HUBs.
# 
# WARNING: NON-FREE AND DISTRIBUTION RESTRICTED ENCUMBERED SOFTWARE IN IMAGE.
#
# The VS Build Tools are very encumbered.
#
# IMAGE SHOULD ONLY BE BUILT BY USERS WHO CAN FOLLOW THESE TERMS:
# https://visualstudio.microsoft.com/license-terms/mlt553512/
#
# See the fourth paragraph of this blog post for a warning from a Microsoftie:
# https://blogs.msdn.microsoft.com/vcblog/2018/08/13/using-msvc-in-a-docker-container-for-your-c-projects/
# 
# """
# The VS Build Tools are licensed as a supplement to your existing Visual Studio license. 
# Any images built with these tools should be for your personal use or for use in your 
# organization in accordance with your existing Visual Studio and Windows licenses. 
# Please don’t share these images on a public Docker hub.
# """
#
# That said, this Dockerfile will be built in a CI system for validation and testing in the project 
# but most definitely will skip deployment.

FROM mcr.microsoft.com/windows/servercore:ltsc2016

SHELL ["powershell", "-Command", "$ErrorActionPreference = 'Stop'; $ProgressPreference = 'SilentlyContinue';"]

RUN $url = 'https://download.visualstudio.microsoft.com/download/pr/10930955/e64d79b40219aea618ce2fe10ebd5f0d/vs_BuildTools.exe'; `
    $sha256 = '68A678D64704A4592CA003494B9C128EE5D2B381672DE174BAF9811E2A84EE72'; `
    Invoke-WebRequest -Uri $url -OutFile C:\vs_BuildTools.exe; `
    $actual256 = (Get-FileHash vs_BuildTools.exe -Algorithm sha256).Hash; `
    if ($actual256 -ne $sha256) { `
       Write-Host 'FAILED!'; `
       Write-Host ('expected: {0}' -f $sha256); `
       Write-Host ('got:      {0}' -f $actual256); `
       exit 1; `
    };    

RUN Start-Process -filepath C:\vs_buildtools.exe -passthru -wait -argumentlist `
    '--add Microsoft.VisualStudio.Workload.VCTools --includeRecommended --quiet --nocache --wait'; `
    Remove-Item C:\vs_BuildTools.exe; `
    Remove-Item -Force -Recurse 'C:\\Program Files (x86)\\Microsoft Visual Studio\\Installer'

ENV RUSTUP_HOME=C:\rustup `
    CARGO_HOME=C:\cargo `
    RUST_VERSION=1.30.1

RUN [Net.ServicePointManager]::SecurityProtocol = [Net.ServicePointManager]::SecurityProtocol -bor [Net.SecurityProtocolType]::Tls12; `
    $url = 'https://static.rust-lang.org/rustup/archive/1.14.0/x86_64-pc-windows-msvc/rustup-init.exe'; `
    Write-Host ('Downloading {0} ...' -f $url); `
    Invoke-WebRequest -Uri $url -OutFile 'rustup-init.exe'; `
    `
    $sha256 = 'c5cd27bbb48b2f6190b32d5524c4e89b2ecab8aa5cfd66085c24e40cc55f6a31'; `
    Write-Host ('Verifying sha256 ({0}) ...' -f $sha256); `
    if ((Get-FileHash rustup-init.exe -Algorithm sha256).Hash -ne $sha256) { `
       Write-Host 'FAILED!'; `
       exit 1; `
    }; `
    `
    New-Item $env:CARGO_HOME\bin -type directory | Out-Null; `
    `
    $newPath = ('{0}\bin;{1}' -f $env:CARGO_HOME, $env:PATH); `
    Write-Host ('Updating PATH: {0}' -f $newPath); `
    [Environment]::SetEnvironmentVariable('PATH', $newPath, [EnvironmentVariableTarget]::Machine); `
    `
    C:\rustup-init.exe -y --no-modify-path --default-toolchain $env:RUST_VERSION --default-host x86_64-pc-windows-msvc; `
    Remove-Item C:\rustup-init.exe
