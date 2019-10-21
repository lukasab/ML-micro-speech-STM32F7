# Compilar em um ARM Cortex-M uma rede neural para detectar comandos de voz

O material foi traduzido e desenvolvido com base no [tutorial da ARM](https://developer.arm.com/solutions/machine-learning-on-arm/developer-material/how-to-guides/build-arm-cortex-m-voice-assistant-with-google-tensorflow-lite/getting-started).

O código adquire amostra de áudio do microfone integrado do STM32F7. O áudio passa por uma transformada rápida de Fourier para criar um espectograma. O espectograma é introduzido em um modelo de machine learning pré-treinado. O modelo usa uma rede neural convolucional para identificar se a amostra representa o comando "yes", "no", silêncio, ou desconhecido.

# Requisitos

Recomendamos que sigam o tutorial em um sistema Linux. Os comando nas próximas seções serão para o terminal _bash_.

- [Git](https://git-scm.com/);
- [Mercurial](https://www.mercurial-scm.org/);
- [pyenv](https://github.com/pyenv/pyenv): para gerenciar a versão do python (2.7);
- [pipenv](https://github.com/pypa/pipenv): para gerenciar as dependências;
- [Mbed CLI](https://github.com/ARMmbed/mbed-cli);
- [STM327 discovery kit](https://os.mbed.com/platforms/ST-Discovery-F746NG/?_ga=2.266004431.1749276604.1570542574-1770900838.1570220510).

Caso você já tenha esses requisistos instalados pode pular para a seção [Começando](#Começando).

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
git config --global user.email "seu.email@gmail.com"
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

Caso você use _Zsh_, troque `~/.bash_profile` por `~/.zshenv`

Caso você use Ubuntu, troque `~/.bash_profile` por `~/.bashrc`

Adicione o `pyenv init` para o _Shell_ com:
```bash
echo -e 'if command -v pyenv 1>/dev/null 2>&1; then\n  eval "$(pyenv init -)"\nfi' >> ~/.bash_profile
```
Caso você use _Zsh_, troque `~/.bash_profile` por `~/.zshenv`

Caso você use Ubuntu, troque `~/.bash_profile` por `~/.bashrc`

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

Defina a versão do python instalada como a padrão para esta pasta com:

```bash
pyenv local 2.7.16
```

## Pipenv

Instale pipenv com:

```bash
pip install pipenv
```

Verifique se pipenv foi instalado corretamente com

```bash
pipenv --version
```

O comando deve ser reconhecido respondendo com algo parecido a: `pipenv, version 2018.11.26`.

Ainda dentro da pasta `C106` execute para criar e ativar um ambiente virtual:

```bash
pipenv install && pipenv shell
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
mbed --help
```

### Baixar o compilador

Baixe o compilador deste [link](https://developer.arm.com/tools-and-software/open-source-software/developer-tools/gnu-toolchain/gnu-rm/downloads) em Downloads.

Extraia o compilador com:
```bash
tar xjf ~/Downloads/gcc-arm-none-eabi-8-2019-q3-update-linux.tar.bz2 -C ~/
```

Talvez a versão baixada mude e portanto o nome do arquivo também. Verifique antes de rodar o comando a cima.

Coloque o compilado no PATH com: 
 
```bash
echo 'export PATH="$PATH:~/gcc-arm-none-eabi-8-2019-q3-update/bin"' >> ~/.bash_profile
exec "$SHELL"
```

Caso você use _Zsh_, troque `~/.bash_profile` por `~/.zshenv`

Caso você use Ubunto, troque `~/.bash_profile` por `~/.bashrc`

Verifique que tudo está funcionando corretamente utilizando o comando abaixo:

```bash
arm-none-eabi-gcc
```

O comando deve ser reconhecido e a seguinte resposta deve ser obtida: 

```bash
arm-none-eabi-gcc: fatal error: no input files
compilation terminated.
```

### Configure o compilador para o Mbed

Por fim, configure o compilador do Mbed com:

```bash
mbed config -G GCC_ARM_PATH	"~/gcc-arm-none-eabi-8-2019-q3-update/bin/arm-none-eabi-gcc"
```

# Começando

Com o ambiente virtual criado e ativado, clone o repositório do TensorFlow com

```bash
git clone https://github.com/tensorflow/tensorflow.git
```

Assim que o projeto o download for concluído, você pode executar o seguinte comando para navegar no diretório do projeto e buildar ele:

```bash
cd tensorflow

make -f tensorflow/lite/experimental/micro/tools/make/Makefile TARGET=mbed TAGS="disco_f746ng" generate_micro_speech_mbed_project
```

Isso vai criar uma pasta em `tensorflow/lite/experimental/micro/tools/make/gen/mbed_cortex-m4/prj/micro_speech/mbed` contendo o código fonte, os arquivos header, os driver Mbed e um README.

Segue abaixo as descrições de alguns arquivos interessantes:

- `disco_f746ng/audio_provider.cc` adquire a amostra de áudio do microfone interno.
- `micro_features/micro_features_generator.cc` usa a transformada rápida de Fourier para criar um espectograma do áudio.
- `micro_features/tiny_conv_micro_features_model_data.cc` esse arquivo é o modelo de machine learning, representado por um grande array com valores do tipo unsigned char.
- `command_responder.cc` é chamado toda vez que é identificado um potencial commando de voz.
- `main.cc` esse arquivo é o ponto de entrada do programa Mbed, que roda o modelo de machine learning usando TensorFlow Lite para microcontroladores.

Para configurar o programa Mbed e baixar as depêndencias rode:

```bash
cd tensorflow/lite/experimental/micro/tools/make/gen/mbed_cortex-m4/prj/micro_speech/mbed
mbed config root .
mbed deploy
```

Como o TensorFlow exige C++ 11, você tera que atualizar o seu perfil para refletir isso. Aqui tem um pequeno script em Python que faz isso. Rode isso na linha de comando:

```bash
python -c 'import fileinput, glob;

for filename in glob.glob("mbed-os/tools/profiles/*.json"):

  for line in fileinput.input(filename, inplace=True):

    print line.replace("\"-std=gnu++98\"","\"-std=c++11\", \"-fpermissive\"")'
```

Depois que essas configurações forem atualizadas você pode compilar o projeto com:

```bash
mbed compile -m DISCO_F746NG -t GCC_ARM
```

# Executando

Agora que a compilação completou, você pode implementar o binario na placa STM32F7 e testar para ver se funciona.

Conecte a placa STM32F7 via USB. A placa deve aparecer na sua máquina como o espaço de disco. Copie o arquivo binário criado com o último comando para dentro da pasta da placa. O binário está em `/BUILD/DISCO_F746NG/GCC_ARM/`. Ou utilize o seguinte comando

```bash
cp ./BUILD/DISCO_F746NG/GCC_ARM/mbed.bin /Volumes/DIS_F746NG/
```

Quando você copiar o arquivo, a placa deve reiniciar com o programa rodando, você deve ver o programa agora interpretando os comandos de voz.

# Resolução de problemas

#to-do
