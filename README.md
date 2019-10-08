# Compilar em um ARM Cortex-M uma rede neural para detectar comandos de voz

O material foi traduzido e desenvolvido com base no [tutorial da arm](https://developer.arm.com/solutions/machine-learning-on-arm/developer-material/how-to-guides/build-arm-cortex-m-voice-assistant-with-google-tensorflow-lite/getting-started).

# Requisitos

Recomendamos que sigam o tutorial em um sistema Linux. Os comando nas próximas seções serão para o terminal _bash_.

- [Git](https://git-scm.com/);
- [Mercurial](https://www.mercurial-scm.org/);
- [pyenv](https://github.com/pyenv/pyenv): para gerenciar a versão do python (2.7);
- [pipenv](https://github.com/pypa/pipenv): para gerenciar as dependências;
- [Mbed CLI](https://github.com/ARMmbed/mbed-cli);
- [STM327 discovery kit](https://os.mbed.com/platforms/ST-Discovery-F746NG/?_ga=2.266004431.1749276604.1570542574-1770900838.1570220510).

Caso você já tenha esses requisistos instalados pode pular para a seção .

# Instalação

## Git

Instale com:

```bash
sudo apt-get install git
```

Configure com:

```bash
git config --global user.name "Seu Nome completo"
```

```bash
git config --global user.email "seu.email@gmail.com
```

## Mercurial

Instale com:

```bash
sudo apt-get install mercurial
```

##  Pyenv

Baixe com:
```bash
git clone https://github.com/pyenv/pyenv.git ~/.pyenv
```
Defina a ambiente de variável no _bash_ com: 
```bash
echo 'export PYENV_ROOT="$HOME/.pyenv"' >> ~/.bash_profile
echo 'export PATH="$PYENV_ROOT/bin:$PATH"' >> ~/.bash_profile
```

Caso vc use _Zsh_, troque `~/.bash_profile` por `~/.zshenv`

Adicione o `pyenv init` para o _Shell_ com:
```bash
echo -e 'if command -v pyenv 1>/dev/null 2>&1; then\n  eval "$(pyenv init -)"\nfi' >> ~/.bash_profile
```

Reinicie o _Shell_ para aplicar as modificações com:

```bash
exec "$SHELL"
```

Verifique que a instalação deu certo com:

```bash
pyenv -v
```

O comando deve ser reconhecido respondendo com algo parecido a: `pyenv 1.2.13-12-g8a56fe64` 

Instale a versão 2.7 do python que usaremos nesse tutorial com:

```bash
pyenv install 2.7.16
```

Crie uma pasta para o tutorial:

```bash
mkdir C106
cd C106
```

Defina versão do python instala como a padrão para está pasta com:

```bash
pyenv local 2.7.16
```

## Pipenv

Instale pipenv com:

```bash
sudo apt install pipenv
```

Verifique que pipenv foi instalado corretamente com

```bash
pipenv --version
```

O comando deve ser reconhecido respondendo com algo parecido a: `pipenv, version 2018.11.26`.

Ainda dentro da pasta `C106` execute para criar e ativar um ambiente virtual:

```bash
pipenv shell
pipenv install
```

Verifique que a versão do python está correta executando:

```bash
python -V
```
A seguinte resposta deverá ser obtida `Python 2.7.16`.

## Mbed-cli

Instale Mbed CLI com pip:

```bash
pip install mbed-cli
```

Para verificar que Mbed CLI instalou corretamente, run

```bash
mbed --help.
```

### Baixar o compilador

Baixe o compilador deste [link](https://developer.arm.com/tools-and-software/open-source-software/developer-tools/gnu-toolchain/gnu-rm/downloads) em Downloads.

Extraia o compilador com:
```bash
tar xjf ~/Downloads/gcc-arm-none-eabi-8-2019-q3-update-linux.tar.bz2 -C ~/
```

Talvez a versão baixada mude e portanto o nome do arquivo também. Verifique antes de rodar o comando a cima.

Coloque o compilado no PATH com:
$ 
$ 
```bash
echo 'export PATH="$PATH:~/gcc-arm-none-eabi-8-2019-q3-update/bin"' >> ~/.bash_profile
exec "$SHELL"
```

Caso vc use _Zsh_, troque `~/.bash_profile` por `~/.zshenv`

Verifique que tudo deu certo com:

```bash
arm-none-eabi-gcc
```

O comando deve ser reconhecido e a seguinte resposta deve ser obtida: 

```bash
arm-none-eabi-gcc: fatal error: no input files
compilation terminated.
```

### Configure o compilador para o mbed

Por fim, configure o compilador do mbed com:

```bash
mbed config -G GCC_ARM_PATH	"~/gcc-arm-none-eabi-8-2019-q3-update/bin/arm-none-eabi-gcc"
```