# Instalação Exherbo Linux PT-BR (SOMENTE BIOS/LEGACY) ![Linux](https://img.shields.io/badge/Linux-FCC624?style=for-the-badge&logo=linux&logoColor=black)
Aprenda a instalar uma das distribuições mais difíceis do mundo Linux.

De fato, o Exherbo Linux é uma distro de você chorar enquanto instala, os criadores acham a instalação do Gentoo **fácil**.<br>

Eu passei um sufoco essa madrugada tentando dar boot, meu amigo tinha conseguido, porém congelou na hora da inicialização do Kernel.<br>

Se você for novo no Linux, evite essa distribuição, **por enquanto** isso aqui não é pra você, melhore suas habilidades e especialize! Use um debian-based, arch e volte aqui quando tiver uma boa carga.<br>

Até então o próprio usuário de Gentoo passa dificuldade (Eu, por exemplo, haha.)<br>
É complicado, **mas eu sei que você vai conseguir!**<br>

**Então segue aqui um tutorial descomplicado, baseado em wikis e na própria página de instalação do Exherbo:**

```1 - Baixe um LiveCD```<br>

Bom, você irá precisar usar um LiveCD para fazer a instalação do Exherbo Linux.
Não, o Exherbo não é uma ISO! O Exherbo é um .tar.xz com todo o sistema, o LiveCD servirá para configurar ele em seu HD.
Você pode usar um Arch Live ISO, ou o SystemRescueHD, são os que eu recomendo.

```2 - Preparando o seu HD```<br>

Após finalmente inicializar seu LiveCD via Pendrive, etc.. Você irá preparar o seu HD.
Ìnicialize o gerenciador de partições do sistema, como de praxe, usaremos o cfdisk.

`cfdisk /dev/sda`<br>

Crie uma partição de boot (~500MB), a partição root (/) (>=4GB), e outras partições como /home, caso queira.
Criaremos também uma partição de swap. Salve e saia.

Agora, precisamos formatar as partições:
Considerando /dev/sda1 como **boot**, /dev/sda2 como o **swap** e /dev/sda3 como **root**, faremos:

`mkfs.ext2 /dev/sda1`<br>

`mkswap /dev/sda2 && swapon /dev/sda2`<br>

`mkfs.ext4 /dev/sda3`<br><br><br>
`3 - Montando a partição root e colocando o sistema nela`<br>

Agora que todas as partições foram criadas e devidamente formatas, vamos montar a partição **root** e colocar o Exherbo Linux nela:

`mkdir /mnt/exherbo` Crie essa pasta, é onde será montado o /dev/sda3 (Até então considerado como a partição root)<br><br>
`mount /dev/sda3 /mnt/exherbo` Agora foi montada a partição root.<br><br>
`cd /mnt/exherbo` Por fim, entre na pasta.<br><br>

`4 - Baixando o sistema`<br>

Aqui, o seu LiveCD deverá estar conectado na internet, pois iremos fazer o download do Exherbo.
<br><br>(Dica rápida caso você use wifi:<br>
`wpa_passphrase <ssid> <senha> >> /etc/wpa_supplicant/wpa_supplicant.conf`<br><br>
`wpa_supplicant -B -i wlan0 (Ou wlp2s0/Qualquer outra, depende da placa de rede, verifique) -c /etc/wpa_supplicant/wpa_supplicant.conf`<br><br>
`dhcpcd` <br>Conectado!)

Vamos dar um curl no site do Exherbo:<br>
`curl -O https://dev.exherbo.org/stages/exherbo-x86_64-pc-linux-gnu-current.tar.xz`<br>
Após a instalação, vamos fazer o checksum:<br>
`curl -O https://dev.exherbo.org/stages/sha1sum && grep exherbo-x86_64-pc-linux-gnu-current.tar.xz sha1sum | sha1sum -c`<br>
Se estiver tudo certo com o checksum, prossiga, se não, retorne e instale novamente.<br>
Extraia o sistema:<br>
`tar xJpf exherbo*xz`
