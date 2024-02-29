# limpar-cache
Limpar arquivos do computador







@echo off
chcp 65001 > nul
setlocal

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
    echo Pressione qualquer tecla para fechar este programa...
    echo.

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
echo Limpando o diretório temporário: %TempDir%

rem Verificar se o diretório temporário existe
if not exist "%TempDir%" (
    echo O diretório temporário não existe.
    goto :eof
)

rem Limpar arquivos
del /q "%TempDir%\*.*" /s /f > nul 2>&1

rem Limpar pastas
for /d %%x in ("%TempDir%\*") do (
    rd /s /q "%%x" > nul 2>&1
)

echo Limpeza concluída.
echo.

goto :eof
