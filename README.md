// ...existing code...
{ 
# server-gtnh

Servidor GTNH 2.8.1 — scripts de instalação, configuração e execução do servidor Crafty (Crafty Control Panel v4).

## Visão geral do repositório

- Instalador Crafty:
  - [crafty-installer-4.0/.gitignore](crafty-installer-4.0/.gitignore)
  - [crafty-installer-4.0/config.json](crafty-installer-4.0/config.json)
  - [crafty-installer-4.0/install_crafty.py](crafty-installer-4.0/install_crafty.py)
  - [crafty-installer-4.0/install_crafty.sh](crafty-installer-4.0/install_crafty.sh)
  - [crafty-installer-4.0/linux_versions.json](crafty-installer-4.0/linux_versions.json)
  - [crafty-installer-4.0/LICENSE](crafty-installer-4.0/LICENSE)
  - IDE configs: [crafty-installer-4.0/.idea/.gitignore](crafty-installer-4.0/.idea/.gitignore), [crafty-installer-4.0/.idea/vcs.xml](crafty-installer-4.0/.idea/vcs.xml), [crafty-installer-4.0/.idea/inspectionProfiles/](crafty-installer-4.0/.idea/inspectionProfiles/)

- Scripts de plataforma / helpers (pasta `app/`):
  - Instalação por distro: [app/arch.sh](app/arch.sh), [app/centos.sh](app/centos.sh), [app/debian_11.sh](app/debian_11.sh), [app/debian_12.sh](app/debian_12.sh), [app/fedora.sh](app/fedora.sh), [app/linuxmint_21_2.sh](app/linuxmint_21_2.sh), [app/linuxmint_21_3.sh](app/linuxmint_21_3.sh), [app/linuxmint_22.sh](app/linuxmint_22.sh), [app/pop_22_04.sh](app/pop_22_04.sh), [app/raspbian_11.sh](app/raspbian_11.sh), [app/raspbian_12.sh](app/raspbian_12.sh)
  - Utilitários e helpers: [app/helper.py](app/helper.py), [app/pretty.py](app/pretty.py), [app/pip_install_req.sh](app/pip_install_req.sh)

- Execução do servidor Minecraft / Crafty:
  - Systemd service: [minecraft/crafty.service](minecraft/crafty.service)
  - Scripts de execução/atualização: [minecraft/run_crafty.sh](minecraft/run_crafty.sh), [minecraft/run_crafty_service.sh](minecraft/run_crafty_service.sh), [minecraft/update_crafty.sh](minecraft/update_crafty.sh)
  - Diretório do painel Crafty: [minecraft/crafty-4/](minecraft/crafty-4/)

## Requisitos mínimos

- Sistema compatível: distribuições listadas em [crafty-installer-4.0/linux_versions.json](crafty-installer-4.0/linux_versions.json)
- Python 3.x (para [crafty-installer-4.0/install_crafty.py](crafty-installer-4.0/install_crafty.py) e [app/helper.py](app/helper.py))
- Acesso root / sudo para instalações e configuração de serviços systemd
- **Playit.gg** (essencial para exposição de portas e acesso externo ao servidor)

## Instalação rápida (exemplo)

1. Verifique e ajuste [crafty-installer-4.0/config.json](crafty-installer-4.0/config.json) conforme necessário.
2. Executar instalador (bash):
   - Via script shell:
     - sudo bash crafty-installer-4.0/install_crafty.sh
   - Ou via Python:
     - sudo python3 crafty-installer-4.0/install_crafty.py
3. Dependências Python:
   - sudo bash app/pip_install_req.sh

(Os scripts de distro em `app/` cuidam de pacotes específicos por distribuição.)

## Iniciar / Atualizar serviço Crafty

- Iniciar manual:
  - sudo bash minecraft/run_crafty.sh
- Instalar/usar systemd:
  - Copiar/validar [minecraft/crafty.service](minecraft/crafty.service) em /etc/systemd/system/
  - sudo systemctl daemon-reload
  - sudo systemctl enable --now crafty.service
- Atualizar painel:
  - sudo bash minecraft/update_crafty.sh

## Estrutura e customização

- Ajustes de aparência/saída em [app/pretty.py](app/pretty.py).
- Lógica utilitária em [app/helper.py](app/helper.py).
- Configure deploy/instalação por distro usando os scripts dentro de [app/].

## Licença

Consulte [crafty-installer-4.0/LICENSE](crafty-installer-4.0/LICENSE) para detalhes de licença.

## Exposição de portas com Playit.gg

**O Playit.gg é ESSENCIAL** para permitir acesso externo ao servidor GTNH. Ele cria túneis que expõem as portas do servidor para a internet sem necessidade de port forwarding no roteador.

### Instalação do Playit.gg

```bash
# Baixar o cliente playit
curl -SsL https://playit-cloud.github.io/ppa/key.gpg | sudo apt-key add -
sudo curl -SsL -o /etc/apt/sources.list.d/playit-cloud.list https://playit-cloud.github.io/ppa/playit-cloud.list
sudo apt update
sudo apt install playit

# Ou instalação manual
wget https://github.com/playit-cloud/playit-agent/releases/latest/download/playit-linux-amd64
chmod +x playit-linux-amd64
sudo mv playit-linux-amd64 /usr/local/bin/playit
```

### Configuração e uso do Playit

1. **Iniciar o Playit pela primeira vez:**
   ```bash
   playit
   ```
   - Acesse o link fornecido no terminal
   - Faça login/cadastre-se no Playit.gg
   - Autorize o agente

2. **Expor portas do servidor Minecraft:**
   ```bash
   # Porta padrão do Minecraft (GTNH)
   playit add-tunnel minecraft 25565
   
   # Porta do Crafty Control Panel (interface web)
   playit add-tunnel https 8443
   ```

3. **Executar Playit em background:**
   ```bash
   # Via screen
   screen -dmS playit playit
   
   # Ou via systemd (recomendado)
   sudo systemctl enable --now playit
   ```

4. **Verificar túneis ativos:**
   ```bash
   playit status
   playit list-tunnels
   ```

5. **Gerenciar túneis:**
   ```bash
   # Remover túnel
   playit remove-tunnel <tunnel-id>
   
   # Ver logs
   playit logs
   ```

### Portas utilizadas no servidor

- **25565** - Servidor Minecraft GTNH (TCP)
- **8443** - Crafty Control Panel Web Interface (HTTPS)
- Portas adicionais podem ser necessárias para mods específicos

### Importante

- Mantenha o agente Playit sempre em execução para o servidor ficar acessível
- Anote os endereços gerados pelo Playit (ex: `seu-servidor.playit.gg:12345`)
- Configure o `server.properties` do Minecraft com a porta correta
- Os jogadores devem usar o endereço fornecido pelo Playit para conectar

## Dicas e solução de problemas

- Logs systemd: `sudo journalctl -u crafty.service -f`
- Logs do Playit: `playit logs` ou `sudo journalctl -u playit -f`
- Verifique permissões dos scripts e paths antes de executar
- Para debug rápido, execute os scripts com `bash -x` ou Python em modo verboso
- Se o servidor não estiver acessível externamente, verifique se o Playit está rodando: `playit status`
- Teste conectividade local primeiro: `telnet localhost 25565`

} 
// ...existing code...
