# Debian 12 Minimal com LVM + Snapshots + Docker Homelab

## 🧹 Limpeza do disco antes da instalação

Antes de iniciar a instalação do Debian 12, é recomendado limpar completamente o disco para evitar partições antigas ou problemas na criação da tabela de partição.

✅ **Alternativa 1: Usar `dd` no terminal do instalador**

Se `wipefs` não estiver disponível, use o comando `dd` para apagar os primeiros setores do disco:

1. Pressione `Ctrl + Alt + F2` para abrir um terminal no instalador.
2. Execute o comando abaixo:

```bash
dd if=/dev/zero of=/dev/sda bs=1M count=10
```

> ⚠️ Isso irá remover todas as partições e dados do disco `/dev/sda`. Use com cuidado.

3. Verifique com:

```bash
lsblk
```

4. Volte para o instalador com `Ctrl + Alt + F1`.
5. Agora selecione:
   ```
   [ ] SCSI1 (0,0,0) (sda) - 120.0 GB ATA SSD
   ```
   E a opção:
   ```
   → Yes – Create a new empty partition table on this device
   ```
   deve estar disponível.

# Homelab Setup - Debian 12 Minimal com LVM e Snapshots

## Instalação do Sistema com LVM

### Etapa: Instalador do Debian — Particionamento Manual com LVM

Quando chegar em "Partition disks":

### 1. Selecione:
```
Manual
```

### 2. Selecione o disco:
```
[ ] SCSI1 (0,0,0) (sda) - 120.0 GB ATA SSD
```

### 3. Confirme:
```
Yes – Create a new empty partition table on this device
```

### 4. Crie a partição para o LVM:
```
> FREE SPACE (120.0 GB)
  > Create a new partition
```
- Size: `119 GB`
- Type: `Primary`
- Location: `Beginning`
- Use as: `physical volume for LVM`
- Selecione: `Done setting up the partition`

### 5. Configure o LVM:
Na tela principal de particionamento, selecione:
```
Configure the Logical Volume Manager
```

### 6. Confirme:
```
Write the changes to the disk and configure LVM? → Yes
```

### 7. Criar volume group (VG):
```
Create volume group → homelab-vg
→ selecione /dev/sda1
```

### 8. Criar volumes lógicos (LVs):

#### LV: root
```
Create logical volume
→ Volume group: homelab-vg
→ Name: root
→ Size: 25 GB
```

#### LV: home
```
Create logical volume
→ Volume group: homelab-vg
→ Name: home
→ Size: 50 GB
```

#### LV: var (opcional)
```
Create logical volume
→ Volume group: homelab-vg
→ Name: var
→ Size: 20 GB
```

> Deixe ~20 GB livres no VG para snapshots futuros.

### 9. Defina sistemas de arquivos:

#### root
```
→ /dev/mapper/homelab--vg-root
→ Use as: ext4
→ Mount point: /
```

#### home
```
→ /dev/mapper/homelab--vg-home
→ Use as: ext4
→ Mount point: /home
```

#### var
```
→ /dev/mapper/homelab--vg-var
→ Use as: ext4
→ Mount point: /var
```

### 10. Finalize particionamento:
- Se sobrou espaço fora do LVM (~1 GB), crie uma partição swap.
- Caso contrário, pode ignorar.

Selecione:
```
Finish partitioning and write changes to disk
→ Yes
```

---

