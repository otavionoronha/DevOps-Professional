# Use a imagem base do Windows Server 2022
FROM mcr.microsoft.com/windows/server:ltsc2022

# Carrega variaveis de ambiente
ENV ORACLE_HOME="C:\oracle_client"
ENV JAVA_HOME="C:\java\bin"
ENV PATH="C:\Windows\system32;C:\Windows;C:\Windows\System32\Wbem;C:\Windows\System32\WindowsPowerShell\v1.0\;C:\Windows\System32\OpenSSH\;C:\Users\ContainerAdministrator\AppData\Local\Microsoft\WindowsApps;$ORACLE_HOME;$JAVA_HOME"

# Instale as funções do IIS
RUN powershell -Command \
	Add-WindowsFeature Web-Server ; \
	Add-WindowsFeature Web-Log-Libraries ; \
	Add-WindowsFeature Web-Request-Monitor ; \
	Add-WindowsFeature Web-Http-Tracing ; \
	Add-WindowsFeature Web-Windows-Auth ; \
	Add-WindowsFeature Web-App-Dev ; \
	Add-WindowsFeature Web-Net-Ext45 ; \
	Add-WindowsFeature Web-Asp-Net45 ; \
	Add-WindowsFeature NET-Framework-Features ; \
	Add-WindowsFeature NET-Framework-45-Features ; \
	Add-WindowsFeature NET-Framework-45-Core ; \
	Add-WindowsFeature NET-Framework-45-ASPNET ; \
	Add-WindowsFeature NET-WCF-Services45 ; \
	Add-WindowsFeature NET-WCF-TCP-PortSharing45 ; \
	Add-WindowsFeature WoW64-Support ;


# Diretorio das fotes
WORKDIR /Windows/Fonts
COPY "./Pre-Req/Exo_2/*" ./
COPY "./Pre-Req/Segoe_UI/*" ./

# Defina o diretório de trabalho
WORKDIR /install
# Copie o arquivo ZIP do Oracle Client para o contêiner
COPY ./Pre-Req/jre-8u441-windows-x64.exe /install
COPY ./Pre-Req/VC_redist.x64.exe /install
COPY ./oracle-client/ /oracle_client
COPY ./Pre-Req/wkhtmltox-0.12.5-1.msvc2015-win64.exe ./

# Instale o Visual C++ Redistributable (se necessário)
RUN powershell -Command .\VC_redist.x64.exe /s
RUN powershell -Command .\wkhtmltox-0.12.5-1.msvc2015-win64.exe /S
RUN powershell -Command Start-Process jre-8u441-windows-x64.exe -ArgumentList "/s","INSTALLDIR=C:\java" -wait

# Execute o instalador do Oracle Client ODBC (ajuste o comando conforme necessário)
WORKDIR /oracle_client
RUN powershell -Command .\odbc_install.exe /s

# Remover a pasta
RUN powershell -Command Remove-Item -Force C:\\install -Recurse
RUN powershell -Command Remove-Item C:\Users\ContainerAdministrator\AppData\Local\Temp\* -Recurse -Force

# Defina o diretório de trabalho
WORKDIR /inetpub/wwwroot
COPY ./inetpub/wwwroot/ .

# Crie uma pasta persistente
#VOLUME C:\\inetpub\\wwwroot

# Exponha a porta 80 para o IIS
EXPOSE 80

# Inicie o IIS
CMD ["cmd", "/c", "start", "C:\\ServiceMonitor.exe", "w3svc"]