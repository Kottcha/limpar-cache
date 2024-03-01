# Limpar-Cache by: Kottcha

@echo off
chcp 65001 > nul
setlocal

rem Definir o caminho para o arquivo de log
set "LogFile=%~dp0cleanup_log.txt"

rem Abrir o arquivo de log para escrita, limpando o conteúdo anterior, se houver
echo. > "%LogFile%"

rem Obter data e hora atual
for /f "tokens=2 delims==" %%G in ('wmic os get localdatetime /value') do set "datetime=%%G"
set "YYYY=%datetime:~0,4%"
set "MM=%datetime:~4,2%"
set "DD=%datetime:~6,2%"
set "HH=%datetime:~8,2%"
set "Min=%datetime:~10,2%"
set "Sec=%datetime:~12,2%"
set "DateTime=%DD%/%MM%/%YYYY% %HH%:%Min%:%Sec%"

rem Adicionar data e hora ao arquivo de log
echo Registro de limpeza iniciado em %DateTime% >> "%LogFile%"
echo. >> "%LogFile%"

rem Solicitar opção do usuário
set /p opcao=Você deseja iniciar a limpeza? (sim/não): 

rem Verificar a resposta do usuário
if /i "%opcao%"=="sim" (
    rem Limpeza do diretório temporário do usuário atual
    call :limpar_temp "C:\Users\%USERNAME%\AppData\Local\Temp"

    rem Limpeza do diretório temporário do Windows
    call :limpar_temp "C:\Windows\Temp"

    rem Limpeza do diretório Prefetch do Windows
    call :limpar_temp "C:\Windows\Prefetch"

    rem Mensagem de conclusão
    echo.
    echo Limpando diretórios temporários concluída.
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
echo Limpando o diretório temporário: %TempDir% >> "%LogFile%"

rem Adicionar data e hora ao registro de limpeza
echo Registro de limpeza iniciado em %DateTime% >> "%LogFile%"
echo. >> "%LogFile%"

rem Verificar se o diretório temporário existe
if not exist "%TempDir%" (
    echo O diretório temporário não existe. >> "%LogFile%"
    goto :eof
)

rem Limpar arquivos
for %%A in ("%TempDir%\*.*") do (
    echo Deletando arquivo: %%~nxA >> "%LogFile%"
    del /q "%%A" >> "%LogFile%" 2>&1
)

rem Limpar pastas
for /d %%X in ("%TempDir%\*") do (
    echo Deletando pasta: %%~nX >> "%LogFile%"
    rd /s /q "%%X" >> "%LogFile%" 2>&1
)

echo Limpeza concluída. >> "%LogFile%"
echo. >> "%LogFile%"
goto :eof
