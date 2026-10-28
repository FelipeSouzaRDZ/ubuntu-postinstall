# 🐧 Ubuntu Linux — Guia de Pós-Instalação para Desenvolvedores & Data Science

Este repositório contém o roteiro de pós-instalação do **Ubuntu**, otimizado para criar um ambiente limpo, de alta performance e totalmente preparado para **Desenvolvimento Python moderno, Ciência de Dados, Aprendizado de Máquina (NVIDIA CUDA)** e gravações/transmissão de vídeos.

---

## 📋 Pré-requisitos & Hardware

* **Sistema Operacional:** Ubuntu 26.04 LTS (ou 24.04 LTS)
* **GPU:** NVIDIA GeForce RTX 2060 (ou superior)
* **Requisito de Rede:** Conexão ativa com a internet.

---

## 🛠️ Passo a Passo de Configuração

### 1. Atualização do Sistema e Repositórios Oficiais

Habilita os repositórios restritos e proprietários do Ubuntu e atualiza a base do sistema.

```bash
# Atualiza os índices de pacotes e o sistema
sudo apt update && sudo apt upgrade -y

# Habilita os repositórios multiverse e restricted
sudo add-apt-repository multiverse -y
sudo add-apt-repository restricted -y

```

---

### 2. Drivers NVIDIA (Série 615 Open) & Codecs de Mídia

Instalação dos drivers mais recentes da série **615** (Open Kernel Modules) integrados nativamente no Ubuntu 26.04 para garantir máxima compatibilidade com Wayland e aceleração por GPU.

* O driver NVIDIA é opcional, pois o Ubuntu já instala uma versão estável ao instalar o sistema
```bash
# Instala o driver NVIDIA 615 Open e os módulos de Kernel
sudo apt update
sudo apt install nvidia-driver-615-open nvidia-dkms-615-open -y
```


```bash
# Instala codecs de áudio/vídeo essenciais para gravação de aulas
sudo apt install ubuntu-restricted-extras -y

```



---

### 3. Ferramentas de Compilação & Dependências Base

Instala os compiladores C/C++ (`build-essential`) e os cabeçalhos de desenvolvimento do sistema necessários para compilar bibliotecas e pacotes nativos.

```bash
sudo apt install build-essential git python3-dev -y

```

---

### 4. Gerenciamento do Python com `uv` (Substitui `pip`, `venv` e `pyenv`)

Uso do **`uv`** para instalar e alternar entre múltiplas versões do Python (3.11, 3.12, 3.13, etc.) de forma isolada, leve e ultrarrápida, sem interferir no Python do sistema operacional.

```bash
# 1. Instalação oficial do uv no ambiente de usuário
curl -LsSf https://astral.sh/uv/install.sh | sh
source ~/.bashrc

# 2. Instalação das versões do Python desejadas para projetos
uv python install 3.11 3.12

# 3. Exemplo de uso no dia a dia: criar um venv com versão específica
# uv venv --python 3.11 .venv
# source .venv/bin/activate

```

---

### 5. Docker Oficial & Aceleração por GPU (NVIDIA Toolkit)

Configuração do repositório oficial da Docker Inc. junto com o runtime da NVIDIA para rodar contêineres de Machine/Deep Learning acelerados pela RTX 2060.

```bash
# Adiciona a chave GPG e o repositório oficial do Docker
sudo apt update
sudo apt install ca-certificates curl gnupg -y
sudo install -m 0755 -d /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
sudo chmod a+r /etc/apt/keyrings/docker.gpg

echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "$UBUNTU_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

# Instala o Docker Engine e adiciona seu usuário ao grupo docker
sudo apt update
sudo apt install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin -y
sudo usermod -aG docker $USER

# Adiciona o repositório e instala o NVIDIA Container Toolkit
curl -fsSL https://nvidia.github.io/libnvidia-container/gpgkey | sudo gpg --dearmor -o /usr/share/keyrings/nvidia-container-toolkit-keyring.gpg
curl -s -L https://nvidia.github.io/libnvidia-container/stable/deb/nvidia-container-toolkit.list | \
    sed 's#deb https://#deb [signed-by=/usr/share/keyrings/nvidia-container-toolkit-keyring.gpg] https://#g' | \
    sudo tee /etc/apt/sources.list.d/nvidia-container-toolkit.list

sudo apt update
sudo apt install nvidia-container-toolkit -y
sudo nvidia-ctk runtime configure --runtime=docker
sudo systemctl restart docker

```

---

### 6. VS Code & OBS Studio

```bash
# Adiciona o repositório oficial da Microsoft
wget -qO- https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > packages.microsoft.gpg
sudo install -D -o root -g root -m 644 packages.microsoft.gpg /etc/apt/keyrings/packages.microsoft.gpg
sudo sh -c 'echo "deb [arch=amd64,arm64,armhf signed-by=/etc/apt/keyrings/packages.microsoft.gpg] https://packages.microsoft.com/repos/code stable main" > /etc/apt/sources.list.d/vscode.list'
rm -f packages.microsoft.gpg

# Instala o VS Code (.deb oficial) e o OBS Studio
sudo apt update
sudo apt install code obs-studio -y

```

---

### 7. Tipografia: JetBrains Mono Nerd Font

Download e registro automático da fonte para terminal e VS Code (com suporte a ícones e ligaturas tipográficas).

```bash
mkdir -p ~/.local/share/fonts/JetBrainsMono
cd /tmp
wget https://github.com/ryanoasis/nerd-fonts/releases/latest/download/JetBrainsMono.zip
unzip JetBrainsMono.zip -d ~/.local/share/fonts/JetBrainsMono
rm JetBrainsMono.zip
fc-cache -fv

```

---

## 🔄 Finalização

Após executar os comandos acima, **reinicie o sistema** para carregar os módulos da NVIDIA no Kernel e aplicar as permissões do grupo Docker:

```bash
sudo reboot

```

---

## 🧪 Comandos de Validação (Testes)

1. **Verificar se o Driver NVIDIA 615 e CUDA estão ativos:**
```bash
nvidia-smi

```


2. **Verificar o funcionamento do `uv`:**
```bash
uv --version
uv python list --installed

```


3. **Verificar se o Docker enxerga a GPU:**
```bash
docker run --rm --gpus all nvidia/cuda:12.0.0-base-ubuntu22.04 nvidia-smi

```


4. **Confirmar instalação da fonte:**
```bash
fc-list | grep -i "JetBrainsMono"

```
