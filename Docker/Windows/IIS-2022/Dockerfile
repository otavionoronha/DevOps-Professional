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

# Copie o arquivo ZIP do Oracle Client para o contêiner
ADD https://download.oracle.com/otn_software/nt/instantclient/2370000/instantclient-basic-windows.x64-23.7.0.25.01.zip /install/oracle-basic.zip
ADD https://download.oracle.com/otn_software/nt/instantclient/2370000/instantclient-odbc-windows.x64-23.7.0.25.01.zip /install/oracle-odbc.zip
ADD https://javadl.oracle.com/webapps/download/AutoDL?BundleId=251656_7ed26d28139143f38c58992680c214a5 /install/jre-8u441-windows-x64.exe.exe
ADD https://aka.ms/vs/16/release/vc_redist.x64.exe /install/vc_redist.x64.exe
COPY ./Pre-Req/wkhtmltox-0.12.5-1.msvc2015-win64.exe /install

# Diretorio das fotes
WORKDIR /Windows/Fonts
COPY "./Pre-Req/Exo_2/*" ./
COPY "./Pre-Req/Segoe_UI/*" ./

# Instale o Visual C++ Redistributable (se necessário)
WORKDIR /install
RUN powershell -Command .\VC_redist.x64.exe /s
RUN powershell -Command .\wkhtmltox-0.12.5-1.msvc2015-win64.exe /S
RUN powershell -Command Start-Process jre-8u441-windows-x64.exe.exe -ArgumentList "/s","INSTALLDIR=C:\java" -wait

# Extrair os arquivos do oracle
WORKDIR /oracle_client
RUN powershell -Command Expand-Archive -Path /install/oracle-basic.zip -DestinationPath . -Force
RUN powershell -Command Expand-Archive -Path /install/oracle-odbc.zip -DestinationPath . -Force

# Execute o instalador do Oracle Client ODBC (ajuste o comando conforme necessário)
RUN powershell -Command Move-Item /oracle_client/instantclient_23_7/* /oracle_client
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