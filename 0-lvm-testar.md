# Debian 12 Minimal com LVM + Snapshots + Docker Homelab

## 🧹 Limpeza do disco antes da instalação

Antes de iniciar a instalação do Debian 12, é recomendado limpar completamente o disco para evitar partições antigas ou problemas na criação da tabela de partição.

1. Quando estiver no instalador do Debian, pressione `Ctrl + Alt + F2` para abrir um terminal.
2. Execute o comando abaixo para apagar a tabela de partição do disco:

```bash
wipefs -a /dev/sda
```

> ⚠️ Isso irá remover todas as partições e dados do disco `/dev/sda`. Use com cuidado.

3. Verifique se o disco está limpo com:

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

---

## 🛠️ Instalação do Debian com LVM e Snapshots

### Etapa: Instalador do Debian — Particionamento Manual com LVM

Quando chegar na etapa **"Partition disks"**:

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

### 4. Criar partição física para o LVM:
```
> FREE SPACE (120.0 GB)
  > Create a new partition
```
- **Size**: `119 GB`
- **Type**: `Primary`
- **Location**: `Beginning`
- **Use as**: `physical volume for LVM`
- Selecione: `Done setting up the partition`

### 5. Configurar o LVM:
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

#### LV: swap (opcional)
```
Create logical volume
→ Volume group: homelab-vg
→ Name: swap
→ Size: 1 GB
```

> 💡 Deixe cerca de **20 GB livres no VG** para snapshots futuros.

### 9. Definir sistemas de arquivos:

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

#### var (opcional)
```
→ /dev/mapper/homelab--vg-var
→ Use as: ext4
→ Mount point: /var
```

#### swap (opcional)
```
→ /dev/mapper/homelab--vg-swap
→ Use as: swap area
```

### 10. Finalizar particionamento:
- Se sobrou espaço fora do LVM (~1 GB), crie uma partição swap física.
- Caso contrário, pode ignorar.

Selecione:
```
Finish partitioning and write changes to disk
→ Yes
```

---

