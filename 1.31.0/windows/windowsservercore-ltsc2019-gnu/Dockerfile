# escape=`

FROM mcr.microsoft.com/windows/servercore:ltsc2019

SHELL ["powershell", "-Command", "$ErrorActionPreference = 'Stop'; $ProgressPreference = 'SilentlyContinue';"]

ENV RUSTUP_HOME=C:\rustup `
    CARGO_HOME=C:\cargo `
    RUST_VERSION=1.31.0

RUN [Net.ServicePointManager]::SecurityProtocol = [Net.ServicePointManager]::SecurityProtocol -bor [Net.SecurityProtocolType]::Tls12; `
    $url = 'https://static.rust-lang.org/rustup/archive/1.15.0/x86_64-pc-windows-msvc/rustup-init.exe'; `
    Write-Host ('Downloading {0} ...' -f $url); `
    Invoke-WebRequest -Uri $url -OutFile 'rustup-init.exe'; `
    `
    $sha256 = '155e431c357b1e612b7c91b5802cc9278cbaa776c76ad3591c7dd0b333643fc2'; `
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
    C:\rustup-init.exe -y --no-modify-path --default-toolchain $env:RUST_VERSION --default-host x86_64-pc-windows-gnu; `
    Remove-Item C:\rustup-init.exe
