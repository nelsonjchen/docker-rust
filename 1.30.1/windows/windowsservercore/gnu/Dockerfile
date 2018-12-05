# escape=`

FROM microsoft/windowsservercore:latest

SHELL ["powershell", "-Command", "$ErrorActionPreference = 'Stop'; $ProgressPreference = 'SilentlyContinue';"]

ENV RUSTUP_HOME=C:\rustup `
    CARGO_HOME=C:\cargo `
    RUST_VERSION=1.30.1

RUN [Net.ServicePointManager]::SecurityProtocol = [Net.ServicePointManager]::SecurityProtocol -bor [Net.SecurityProtocolType]::Tls12; `
    $url = 'https://static.rust-lang.org/rustup/archive/1.15.0/x86_64-pc-windows-msvc/rustup-init.exe'; `
    Write-Host ('Downloading {0} ...' -f $url); `
    Invoke-WebRequest -Uri $url -OutFile 'rustup-init.exe'; `
    `
    $sha256 = '155E431C357B1E612B7C91B5802CC9278CBAA776C76AD3591C7DD0B333643FC2'; `
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
    C:\rustup-init.exe -y --no-modify-path --default-toolchain ${RUST_VERSION}} --default-host x86_64-pc-windows-gnu; `
    Remove-Item C:\rustup-init.exe
