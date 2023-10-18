# Protótipo de Classificação de Vídeos com TensorFlow JS

# Raspberry Pi: Webcam conectada na porta USB

![](/readme/RaspPiWebcam.jpg)

### Captura do Vídeo

Instale o ffmpeg:

```bash
sudo apt update
sudo apt install ffmpeg
```

Captura de um vídeo de 10 segundos já no formato MP4

```bash
ffmpeg -f v4l2 -input_format h264 -video_size 1920x1080 -i /dev/video1 -c:v copy -t 10 nome_da_classe.mp4
```

Sim, é simples assim utilizando a camera USB.  
Seja Feliz =D

<hr>

# Raspberry Pi: Pi Camera

## Configuração inicial

![](https://github.com/alexandremendoncaalvaro/severinopi/raw/master/readme_images/connect-camera.gif)

Existem versões mais antigas e mais novas dos dispositivos, e estas serão descritas abaixo:

## Versão Antiga

Para conseguir usar a câmera do Raspberry Pi (Pi Camera) como uma Webcam é necessário fazer algumas configurações:

-   Conecte a Pi Camera corretamente ao seu Raspberry Pi (Conforme o Gif Acima).
-   Ative a câmera com o seguinte comando:

```bash
sudo raspi-config
```

Navegue até Interface Options > Camera (ou Legacy Camera) e selecione Enable.

#### Instalação dos pacotes necessários

Primeiro, atualize os repositórios e o sistema:

```bash
sudo apt update && sudo apt upgrade
```

Em seguida, instale o pacote v4l2loopback-dkms:

```bash
sudo apt install v4l2loopback-dkms
```

Carregando o módulo v4l2loopback:
Execute o comando:

```bash
sudo modprobe v4l2loopback
```

Isso cria um dispositivo de vídeo virtual, geralmente localizado em /dev/video0.
Transmitindo vídeo da Pi Camera para o dispositivo virtual:
Utilize o raspivid para capturar e direcionar o vídeo:

```bash
raspivid -t 0 -l -o /dev/video0
```

Usando o dispositivo virtual como uma webcam:
O dispositivo /dev/video0 agora pode ser usado como uma webcam.Para testar, use o VLC:

```bash
sudo apt install vlc
vlc v4l2:///dev/video0
```

Automatizando a criação do dispositivo virtual:
Se desejar que o Raspberry Pi configure a Pi Camera como webcam sempre que for reiniciado, adicione os comandos relevantes ao seu arquivo .bashrc ou crie um script de inicialização usando systemd.

### Testando Captura do Vídeo

```bash
# Captura de um vídeo de 10 segundos
raspivid -o ~/Desktop/nome_da_classe.h264 -t 10000 --rotation 270 -w 1080 -h 1920
```

```bash
# Conversão para MP4
ffmpeg -f h264 -i ~/Desktop/nome_da_classe.h264 -c copy ~/Desktop/nome_da_classe.mp4
```

<hr>

## Versão Nova

Para versão nova, é preciso preencher alguns pré-requisitos.

## Pré-requisitos

-   Raspberry Pi 4
-   Raspberry Pi OS - Versão 64-bit com Desktop
    -   Pode instalar a imagem [Clicando Aqui](https://downloads.raspberrypi.com/raspios_arm64/images/raspios_arm64-2023-10-10/2023-10-10-raspios-bookworm-arm64.img.xz)
-   Pi Camera
-   Conectar a Pi Camera corretamente ao seu Raspberry Pi.

### Configurando ambiente

Primeiro, atualize os repositórios e o sistema:

```bash
sudo apt update && sudo apt upgrade
```

Por padrão a biblioteca ja vem instalada, mas pro precaução, Instale a LibCamera

```bash
sudo apt install libcamera-tools
```

Verifique se sua camera está funcionando:

```bash
# Abre uma tela com a imagem capturada da sua camera
libcamera-vid -t 0
```

Se você não vir uma prévia da câmera, pode haver um problema com a conexão da câmera ou com a configuração do Raspberry Pi. Verifique as etapas anteriores novamente.

#### Configurando a Pi Camera como WebCam:

Para utilizar a Pi Camera como webcam, você precisará de um software que transforme o stream da câmera em um dispositivo virtual de vídeo. Para isso instalaremos a biblioteca: _v4l2loopback-dkms_.

```bash
sudo apt install v4l2loopback-dkms
```

Carregue o módulo v4l2loopback executando o comando:

```bash
#Isso cria um dispositivo de vídeo virtual, geralmente localizado em /dev/video0.
sudo modprobe v4l2loopback
```

#### Transmitindo vídeo da Pi Camera para o dispositivo virtual:

Agora, você deve ter um novo dispositivo de vídeo em /dev/videoX (onde X é um número, geralmente 0 ou 1 dependendo de outros dispositivos de vídeo em seu sistema).
Redirecione o stream da libcamera-vid para esse dispositivo:

```bash
# Redireciona a imagem capturada como se fosse uma Webcam
libcamera-vid -t 0 --codec=h264 -o - | ffmpeg -i - -vf format=yuv420p -f v4l2 /dev/videoX
```

Agora você ja deve estar apto a utilizar a RaspiCam com seu dispositivo Raspberry Pi **No Mozilla!**

_PS: Caso não esteja funcionando abrindo a camera, Cogite alterar o navegador._
