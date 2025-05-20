# Configuração do Governador de Frequência da CPU no Alpine Linux

Este guia ensina como configurar o governador de frequência "ondemand" para todos os núcleos da CPU automaticamente no boot do Alpine Linux usando `cpufrequtils` e scripts locais.

---

## \:package: Instalar dependências

Instale o utilitário:

```sh
apk add cpufrequtils
```

---

## \:mag: Verificar as informações da CPU

Confira as informações de frequência:

```sh
cpufreq-info
```

Isso deve mostrar os núcleos e os governadores disponíveis.

---

## \:gear: Criar script de inicialização local

Crie o script local que será executado no boot:

```sh
nano /etc/local.d/cpufreq.start
```

Conteúdo do arquivo:

```sh
#!/bin/sh
for cpu in /sys/devices/system/cpu/cpu*/cpufreq/scaling_governor; do
    echo ondemand > "$cpu"
done
```

Dê permissão de execução:

```sh
chmod +x /etc/local.d/cpufreq.start
```

---

## \:rocket: Ativar execução no boot

Adicione o serviço local aos runlevels padrão:

```sh
rc-update add local default
```

Verifique se foi adicionado:

```sh
rc-update show | grep local
```

---

## \:arrows\_clockwise: Reiniciar e testar

Reinicie:

```sh
reboot
```

Após reiniciar, verifique novamente:

```sh
cpufreq-info
```

O governador "ondemand" deve estar ativo para todos os núcleos.

---

## \:white\_check\_mark: Finalizado

Seu Alpine agora está configurado para aplicar o governador "ondemand" automaticamente no boot.

Para verificar mudanças dinâmicas de frequência:

```sh
watch -n1 "cat /proc/cpuinfo | grep MHz"
```

---
