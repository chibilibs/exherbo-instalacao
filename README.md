# Instalação Exherbo Linux PT-BR (BRANCH UEFI) ![Linux](https://img.shields.io/badge/Linux-FCC624?style=for-the-badge&logo=linux&logoColor=black)
Aprenda a instalar uma das distribuições mais difíceis do mundo Linux.

De fato, o Exherbo Linux é uma distro de você chorar enquanto instala, os criadores acham a instalação do Gentoo **fácil**.<br>

Eu passei um sufoco quando tentei instalar, um amigo tinha conseguido, porém congelou na hora da inicialização do Kernel.<br><br>
Um tempo depois consegui de forma bem sucedida fazer a instalação do mesmo, segue a screenshot:

Se você for novo no Linux, evite essa distribuição, **por enquanto**, melhore suas habilidades e especialize-se! Use uma distribuição baseada no debian, ou outra, enfim. 
volte aqui quando tiver uma boa carga. Se você for bem-aventurado... Boa sorte!<br>


**Então segue aqui um tutorial, baseado em wikis, minha experiência e na própria página de instalação do Exherbo:**

**`1 - Baixe um LiveCD`**<br>
Bom, você irá precisar usar um LiveCD para fazer a instalação do Exherbo Linux.
Não, o Exherbo não é uma ISO! O Exherbo é uma tarball (um .tar.xz) com todo o sistema, o LiveCD servirá para configurar ele em seu disco.
Você pode usar um Arch Live ISO, ou o SystemRescueHD, são os que eu recomendo.

**`2 - Preparando o seu Disco`**<br>
Após finalmente inicializar seu LiveCD via Pendrive, etc.. Você irá preparar o seu disco.
Ìnicialize o gerenciador de partições do sistema, como de praxe, usaremos o cfdisk.

`cfdisk /dev/sdX (Ou, se sua tecnologia for nvme... /dev/nvme0n1) -z`<br>

Crie uma label do tipo GPT, com duas partições primárias, a primeira de (~500MB), com o tipo ***EFI System***, e outra do tipo ***Linux Filesystem***, de tamanho inteiro do disco.

Agora, precisamos formatar a partição:

`mkfs.vfat -F32 /dev/sda1`<br><br><br>
`mkfs.ext4 /dev/sda2`<br><br><br>

**`3 - Montando a partição root e colocando o sistema nela`**<br>

Agora que todas a partição foi formatada corretamente, vamos montar a partição **root** e colocar o Exherbo Linux nela:

`mkdir /mnt/exherbo` Crie essa pasta, é onde será montado o /dev/sda1<br><br>
`mount /dev/sda2 /mnt/exherbo` Agora foi montada a partição root.<br><br>
Por ora não montaremos a partição /dev/sda1.<br><br>
`cd /mnt/exherbo` Por fim, entre na pasta.<br><br>

**`4 - Baixando o sistema`**<br>
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
`tar xJpf exherbo*xz`<br>
Arrume seu fstab:<br>
`genfstab -U /mnt/exherbo > /mnt/exherbo/etc/fstab`<br><br>


Agora, monte a partição do tipo EFI /dev/sda1:<br>
`mount /dev/sda1 /mnt/exherbo/boot`<br><br>


**`5 - Entrando no chroot!`**<br>
Agora, você irá de vez entrar no sistema, via chroot.<br>
Vamos então montar as partes essenciais do sistema para que nada dê erro:<br>
`mount --rbind /dev /mnt/exherbo/dev/`<br>
`mount --rbind /sys /mnt/exherbo/sys/`<br>
`mount --rbind /proc /mnt/exherbo/proc/`<br>
Tenha certeza que o sistema vá resolver a internet, ele puxa a Internet do LiveCD, portanto só precisa de resolver:<br>
`cp /etc/resolv.conf /mnt/exherbo/etc/resolv.conf`<br>

Agora é hora de entrar em seu novo sistema.<br>
`env -i TERM=$TERM SHELL=/bin/bash HOME=$HOME $(which chroot) /mnt/exherbo /bin/bash`<br>
<h4>Bem-vindo ao Exherbo Linux :rocket::rocket:</h4><br>
Após dentro do sistema, atualize seu profile:<br>

`source /etc/profile` <br>
Voce pode colocar de maneira opcional, simbolizar que é um chroot:
 `export PS1="(chroot) $PS1" `<br>

**`6 - Atualize o instalador`**<br>
Tenha certeza que o Paludis (Gerenciador de pacotes do Exherbo) esteja configurado<br>
`cd /etc/paludis && vim bashrc && vim *conf`<br><br>
Leia em https://paludis.exherbo.org, a forma correta para configura cada um dos arquivos, no mais você pode:<br>
Em bashrc inserir a flag MAKEOPTS="-jX" (X o número de threads).<br>
Inserir em options.conf: `sys-apps/coreutils xattr`, isso irá adicionar suporte para atributos de arquivos extendidos, que alguns outros pacotes vão precisar usar.<br><br>
Você está na branch UEFI, você precisa compilar o sytemd com a flag EFI, vamos usar systemd-boot. Coloque em options.conf também: `sys-apps/systemd efi`<br><br>
Após feita a configuração dos arquivo de configuração, no vim é `:n`, que pula pro próximo, se alterou algo é `:wn`<br><br>
Agora você pode atualizar sim, o instalador:<br>
`cave resolve paludis -x`<br><br>
`cave sync`<br><br>

(Para instalar um pacote no Paludis é fácil, como exemplo vamos instalar o neovim:<br>
`cave resolve nvim -x`<br>
(Ele não vai executar sem o -x, e talvez essa compilação necessite de algumas flags.)

**`7 - Baixando, configurando e compilando o Kernel.`**<br>
Nessa parte, a configuração do kernel vai depender muito do seu hardware, as configurações serão feitas por você.<br>
Baixe o Kernel que você quiser no https://kernel.org, release, LTS...<br>
Por uma questão de organização, manda pro o kernel pro`/usr/src`<br><br>
`make menuconfig` Pesquise por si próprio sobre **como configurar um kernel**.<br>
Feita as configurações, compile-o:<br>
`make` (Você pode passar um número de threads específico (`make -j36` é o meu caso), dependendo do seu processador e threads, podem demorar de **2 minutos a 6 horas**.<br>
Agora:<br>
`make modules_install`<br>
E então, finalmente...<br>
`make install`<br><br>

Agora, gere o seu initramfs:<br>
`kernel-install add <versão do kernel> /boot/vmlinuz-versão-kernel`<br><br>
_Ex: `kernel-install add 5.14.8 /boot/vmlinuz-5.14.8`_<br><br>

**`8 - Faça bootavel:`**<br>

Ah! Você pode optar reinstalar todos os pacotes do sistema com:<br><br>
`cave resolve world -c` É importante recompilar o conjunto world (main packages) e o -c é flag complete, para que atualize o sistema (Baixado de uma tarball provavelmente desatualizada.)<br><br>
O que você precisa obrigatoriamente reinstalar é o Systemd, pra gerar um ID de máquina válida:<br>
`cave resolve --execute --preserve-world --skip-phase test sys-apps/systemd`<br><br>
Agora, instale o systemd-boot na partição /dev/sda1, montada em /boot:<br>
`bootctl install --esp-path=/boot`<br>
Crie uma entrada que inicie a vmlinuz, o initramfs e o micro-code, caso você tenha instalado. (O da intel é o pacote intel-microcode).<br>
`vim /boot/loader/entries/exherbo.conf`<br>
```bash
title Exherbo Linux
linux /vmlinuz-xxx-xxx
initrd /initramfs-xxx-xxx
/initrd /microcode.cpio # Se houver e você instalou.
options root=/dev/sda2 rw
```<br>
`vim /boot/loader/loader.conf`<br>
```
default exherbo
timeout 3
```<br>
Instale isso aqui também:<br>
`cave resolve hardware -1x`<br>
`cave sync x-hardware`<br>
`cave resolve -x firmware/linux-firmware -x`<br><br>

**`9 - Pós-Instalação`**<br>
Não vou me estender muito aqui, a ideia era ensinar somente a instalação básica, o resto é com você :wink:<br>
`echo "HostnamePika!!!" >> /etc/hostname`<br>
`passwd` Hiper-mega senha da nasa pro root! Ideia: 123 é uma senha forte.<br>
Crie um link simbólico pra sua localização:
`ln -s /usr/share/zoneinfo/America/Sao_Paulo /etc/localtime`<br>
**Em breve mais coisas da pós-Instalação...**<br><br>

**`10 - Fim`**<br>
Se você chegou até aqui, e conseguir bootar, eu te parabenizo! Você é um usuário expert :sunglasses:!!<br>
`reboot && sacrifice a goat && pray`<br><br>
Boa sorte, amigo.

<h3>Manual feito por Sigma (https://github.com/Sigmw)</h3>


