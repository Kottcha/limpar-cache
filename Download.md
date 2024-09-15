@echo off
chcp 65001 > nul
setlocal

rem Definir o caminho para o arquivo de log
set "LogFile=%~dp0cleanup_log.txt"

rem Abrir o arquivo de log para escrita, limpando o conteúdo anterior, se houver
echo. > "%LogFile%"

rem Obter data e hora atual
for /f "tokens=2 delims==" %%G in ('wmic os get localdatetime /value') do set "datetime=%%G"
set "DateTime=%datetime:~6,2%/%datetime:~4,2%/%datetime:~0,4% %datetime:~8,2%:%datetime:~10,2%:%datetime:~12,2%"

rem Adicionar data e hora ao arquivo de log
echo Registro de limpeza iniciado em %DateTime% >> "%LogFile%"
echo. >> "%LogFile%"

rem Solicitar opção do usuário
set /p opcao=Você deseja iniciar a limpeza? (sim/não): 

rem Verificar a resposta do usuário
if /i "%opcao%"=="sim" (
    call :limpar_temp "C:\Users\%USERNAME%\AppData\Local\Temp"
    call :limpar_temp "C:\Windows\Temp"
    call :limpar_temp "C:\Windows\Prefetch"
    call :limpar_temp "C:\Users\%USERNAME%\AppData\Roaming\Microsoft\Windows\Recent"
    call :limpar_temp "C:\Users\%USERNAME%\AppData\Local\Microsoft\Windows\INetCache"
    call :limpar_temp "C:\Users\%USERNAME%\AppData\Local\Microsoft\Windows\Explorer"

    echo.
    echo Limpeza dos diretórios temporários concluída.
    echo Os registros foram salvos em: %LogFile%
    echo Pressione qualquer tecla para fechar este programa...
    echo.
    echo Programa de limpeza by: Kottcha
    pause > nul
    exit
) else if /i "%opcao%"=="não" (
    echo.
    echo Fechando o programa.
    echo.
    pause > nul
    exit
) else (
    echo.
    echo Resposta inválida. Por favor, responda com 'sim' ou 'não'.
    echo.
    pause > nul
    exit
)

:limpar_temp
rem Esta função limpa o diretório temporário especificado
set "TempDir=%~1"
if exist "%TempDir%" (
    echo Limpando o diretório temporário: %TempDir% >> "%LogFile%"
    echo Registro de limpeza iniciado em %DateTime% >> "%LogFile%"
    echo. >> "%LogFile%"
    for %%F in ("%TempDir%\*.*") do (
        del /q /f "%%F"
    )
    echo Diretório %TempDir% limpo. >> "%LogFile%"
) else (
    echo Diretório %TempDir% não encontrado. >> "%LogFile%"
)
goto :eof
