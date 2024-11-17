# Instalando a VPN do Haukins para os CTFs

Este guia ensinará a configurar a VPN do Haukins utilizando uma ferramenta desenvolvida pela nossa entidade. Siga os passos abaixo:

## 1. Clone o repositório da ferramenta

O link para o repositório é: **InstallVpn**

Abra um terminal e clone o repositório para uma pasta de fácil acesso:

```bash
git clone https://github.com/gitinspersec/install_vpn.git
```

Acesse a pasta do repositório:

```bash
cd install_vpn
```

## 2. Baixe a configuração da VPN

- Acesse a plataforma dos CTFs e vá até a aba **Configurações**.
- Clique em **Get a Lab** e escolha a opção **VPN**.
- Clique em **Download VPN config** para baixar o arquivo `wg-conf-1.conf`.

## 3. Mova o arquivo de configuração

Copie o arquivo `wg-conf-1.conf` para a pasta do repositório que você clonou:

```bash
mv /caminho/para/wg-conf-1.conf /caminho/para/install_vpn/
```

Ou simplesmente arraste o arquivo para a pasta do repositório.

## 4. Configure e inicie a VPN

### Se for a primeira vez utilizando a ferramenta:

Dê permissão de execução ao script:

```bash
chmod +x run.sh
```

Execute o script:

```bash
bash ./run.sh
```

### Se já tiver utilizado a ferramenta antes:

Apenas execute o script:

```bash
bash ./run.sh
```

Após a execução do script, a VPN estará configurada e pronta para uso.