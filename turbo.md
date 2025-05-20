# AMD C-60 Undervolt + Turbo Boost no Alpine Linux

Este guia explica como compilar e instalar o programa `undervolt` no Alpine Linux para aplicar undervolt e desbloquear o Turbo Boost do processador AMD C-60.

---

## \:warning: Requisitos

Instale os pacotes necessários:

```sh
apk add build-base linux-headers util-linux git
```

---

## \:floppy\_disk: Clonar este repositório

Clone o repositório com o código fonte:

```sh
git clone https://github.com/axsrza/undervolt.git
cd undervolt
```

---

## \:hammer: Compilar o programa

Compile com:

```sh
make
```

E instale com:

```sh
cp undervolt /usr/bin/
chmod +x /usr/bin/undervolt
```

---

## \:zap: Aplicar o undervolt e ativar turbo

Carregue o módulo MSR e aplique os parâmetros:

```sh
modprobe msr
undervolt -p 0:0x28 -p 1:0x28,3 -p 2:0x38
```

Verifique com:

```sh
undervolt -r
```

Saída esperada:

```
P-state         Vid             Voltage         div
  0             0x28            1.0500V         3.00
  1             0x28            1.0500V         3.00
  2             0x38            0.8500V         5.00
```

---

## \:rocket: Executar automaticamente no boot

Crie um script de inicialização:

```sh
cat << 'EOF' > /etc/init.d/c60-tweak
#!/bin/sh
### BEGIN INIT INFO
# Provides:       c60-tweak
# Default-Start:  3 4 5
# Default-Stop:
### END INIT INFO

modprobe msr
/usr/bin/undervolt -p 0:0x28 -p 1:0x28,3 -p 2:0x38
EOF

chmod +x /etc/init.d/c60-tweak
rc-update add c60-tweak default
```

---

## \:white\_check\_mark: Finalizando

Reinicie o sistema e verifique novamente com:

```sh
undervolt -r
```

Se os P-states estiverem corretos (`0x28`, `3.00`, `0x38`, etc.), o tweak está ativo.

---

## \:link: Repositório

[https://github.com/axsrza/undervolt](https://github.com/axsrza/undervolt)

---

Para dúvidas ou colaborações, abra uma issue no repositório ou envie um PR. \:rocket:
