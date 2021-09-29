# Instalação Exherbo Linux PT-BR (SOMENTE BIOS/LEGACY) ![Linux](https://img.shields.io/badge/Linux-FCC624?style=for-the-badge&logo=linux&logoColor=black)
Aprenda a instalar uma das distribuições mais difíceis do mundo Linux.

De fato, o Exherbo Linux é uma distro de você chorar enquanto instala, os criadores acham a instalação do Gentoo **fácil**.<br>

Eu passei um sufoco essa madrugada tentando dar boot, meu amigo tinha conseguido, porém congelou na hora da inicialização do Kernel.<br><br>
Se eu disser somente: "agora faça isso, depois isso.." é que nem eu sei o que seria exatamente, mas eu sei que funciona e funcionou comigo! :laughing:

Se você for novo no Linux, evite essa distribuição, **por enquanto** isso aqui não é pra você, melhore suas habilidades e especialize! Use um debian-based, ou outra distro, volte aqui quando tiver uma boa carga. Se você for bem-aventurado, Arch-Raíz user, boa sorte pra você também!<br>

Até então o próprio usuário de Gentoo passa dificuldade (Eu por exemplo, :laughing:)<br>
É complicado, **mas eu sei que você vai conseguir!**<br>

**Então segue aqui um tutorial descomplicado, baseado em wikis e na própria página de instalação do Exherbo:**

**`1 - Baixe um LiveCD`**<br>
Bom, você irá precisar usar um LiveCD para fazer a instalação do Exherbo Linux.
Não, o Exherbo não é uma ISO! O Exherbo é um .tar.xz com todo o sistema, o LiveCD servirá para configurar ele em seu HD.
Você pode usar um Arch Live ISO, ou o SystemRescueHD, são os que eu recomendo.

**`2 - Preparando o seu HD`**<br>
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
**`3 - Montando a partição root e colocando o sistema nela`**<br>

Agora que todas as partições foram criadas e devidamente formatas, vamos montar a partição **root** e colocar o Exherbo Linux nela:

`mkdir /mnt/exherbo` Crie essa pasta, é onde será montado o /dev/sda3 (Até então considerado como a partição root)<br><br>
`mount /dev/sda3 /mnt/exherbo` Agora foi montada a partição root.<br><br>
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
Arrume o fstab:<br>
`genfstab -U /mnt/exherbo > /mnt/exherbo/etc/fstab`<br><br>

**`5 - Entrando no chroot!`**<br>
Agora, você irá de vez entrar no sistema, via chroot.<br>
Vamos então montar as partes essenciais do sistema para que nada dê erro:<br>
`mount -o rbind /dev /mnt/exherbo/dev/`<br>
`mount -o rbind /sys /mnt/exherbo/sys/`<br>
`mount /dev/sda1 /mnt/exherbo/boot/` (Montando justamente, até então considerável o boot, no /boot)<br><br>
Tenha certeza que o sistema vá resolver a internet, ele puxa a Internet do LiveCD, portanto só precisa de resolver:<br>
`cp /etc/resolv.conf /mnt/exherbo/etc/resolv.conf`<br>

Agora é hora de "go to deep!"<br>
`env -i TERM=$TERM SHELL=/bin/bash HOME=$HOME $(which chroot) /mnt/exherbo /bin/bash`<br>
<h4>Bem-vindo ao Exherbo Linux :rocket::rocket:</h4><br>
Após estar dentro do seu sistema, atualize o /etc/profile:<br>

`source /etc/profile` <br>
Voce pode colocar de maneira opcional, simbolizar que é um chroot:
 `export PS1="(chroot) $PS1" `<br>

**`6 - Atualize o instalador`**<br>
Tenha certeza que o Paludis (Gerenciador de pacotes do Exherbo, pode ser chamado de cave também.)<br>
`cd /etc/paludis && vim bashrc && vim *conf`<br><br>
Um exemplo nessas configurações são as threads de compilação, por padrão é 2, defina diante do seu computador.<br>
Após feita a configuração de um dos .conf, no vim é `:n`, que pula pro próximo, se alterou algo é `:wn`<br><br>
Agora você pode atualizar o sim, o instalador:<br>
`cave resolve paludis -x`<br><br>
`cave sync`<br><br>

(Para instalar um pacote no Paludis é fácil, como exemplo vamos instalar o neovim:<br>
`cave resolve nvim -x`<br>
Ele não vai executar sem o -x)

**`7 - Baixando, configurando e compilando o Kernel.`**<br>
Nessa parte, a configuração do kernel vai depender muito do seu hardware, as configurações serão feitas por você.<br>
Baixe o Kernel que você quiser no https://kernel.org, release, LTS...<br>
Por uma questão de organização, manda pro o kernel pro`/usr/src`<br><br>
`make menuconfig` Aqui é o cão! Pesquise por si próprio sobre **como configurar um kernel**.<br>
Feita as configurações, compile-o:<br>
`make` (Dependendo do seu processador e threads que você passou no passo 6 em `vim *.conf`, podem demorar de **2 minutos a 6 horas**.<br>
Agora:<br>
`make modules_install`<br>
E então, finalmente...<br>
`make install`<br><br>
**`8 - Faça bootavel:`**<br>

Ah! Você pode optar reinstalar todos os pacotes do sistema com:<br><br>
`cave resolve world -c` Por que isso existe? Eu também não sei, mas demora um tempinho se você não tiver muitas threads, risos.<br><br>
O que você precisa obrigatoriamente reinstalar é o Systemd, pra gerar um ID de máquina válida:<br>
`cave resolve --execute --preserve-world --skip-phase test sys-apps/systemd`<br><br>
Agora, instale o grub no dispositivo /dev/sda (Não é /dev/sda1, 2.. Se trata do **dispositivo**):<br>
`grub-install /dev/sda`<br>
Gere o arquivo de configuração do grub:<br>
`grub-mkconfig -o /boot/grub/grub.cfg`<br>
Instale o boot do Systemd:<br>
`bootctl install`
Agora:<br>
`cave resolve --execute --skip-phase test sys-boot/dracut`<br>
Copie o kernel no diretório apropriado:<br>
`kernel-install add <versão do kernel> /boot/vmlinuz-versão-kernel`<br><br>
_Ex: `kernel-install add 5.14.8 /boot/vmlinuz-5.14.8`_<br><br>
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


