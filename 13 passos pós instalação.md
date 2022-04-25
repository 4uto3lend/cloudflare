# 13 coisas pós instalação ao FreeBSD 13.0
Todos os passos e comandos a seguir serão feitos com o usuário **root**.

#### 1. Alterar porta do ssh
   A nova porta deve ser de pelo menos 5 dígitos (ex: **22334**). Usaremos o editor `ee` para editar o arquivo de configuração:
   `# ee /etc/ssh/sshd_config`

![sshd_config](./images/bsd1.png)

#### 2. Atualizar sistema com os patches mais recentes
   `# freebsd-update fetch install`

![freebsd-update](./images/bsd2.png)

#### 3. Instalar/atualizar árvore Ports
   `# portsnap auto`
![portsnap](./images/bsd3.png)

#### 4. Instalar gerenciador de pacotes `pkg`
   `# /usr/sbin/pkg`

#### 5. Instalar seu editor preferido (vim/nano/etc.)
   `# pkg install -y nano`

A partir de agora, os comandos abaixo podem ser executados com seu editor preferido. O meu é o `vi` :)

> Os editores `vi` e `ee` são padrão na base do FreeBSD

#### 6. Customizar o shell (csh)
Faremos uma configuração padrão e global para todos os usuários do sistema.
1. Remova as linhas de configuração deixando apenas os comentários iniciais. Este é o arquivo individual do usuário **root**.
   `# vi ~./cshrc`
2. O arquivo abaixo será usado para configuração global.
`# vi /etc/csh.cshrc`

Ctrl+C e Ctrl-V no conteúdo abaixo:
```
##
# leonardorosa.com
#
# FreeBSD - custom shell config (csh)



#[ /etc/csh.cshrc ] __ system-wide
setenv	EDITOR	ee	# vi/ee/nano

#[ aliases ]
alias h	history 25
alias j	jobs -l
alias ls	ls -G
alias la	ls -aF
alias ll	ls -lhAF
alias lt	ls -lhTAF

#[ system ]
alias sc	source /etc/csh.cshrc
alias aliases	sudo $EDITOR /etc/csh.cshrc
alias vc	sudo $EDITOR /etc/rc.conf
alias cds	'cd /usr/local/etc/rc.d; ll'
alias du	du -sh
alias dh	df -h
alias rmr	sudo rm -fr
alias iotop	top -m io -o total
alias tlm	tail -n 50 -f /var/log/messages
alias reb	sudo reboot
alias shut	sudo shutdown -r now

#[ network ]
alias ports	sudo sockstat -l
alias ports4	sudo sockstat -4l
alias ports6	sudo sockstat -6l
alias netr	'sudo service netif restart;sudo service routing restart'
alias dhcpr	sudo service dhclient restart lan0

#[ packages ]
alias pkgv	pkg version
alias pkgvv	pkg version -vIL=
alias pkgi	sudo pkg install -y
alias pkgclean	'sudo pkg clean -y;sudo pkg autoremove'
alias pkga	sudo pkg audit -Fr
alias upd	'sudo portsnap auto;sudo freebsd-update fetch install'
alias upg	sudo pkg upgrade -y

#[ dns ]
alias unb	'cd /usr/local/etc/unbound/; ll'
alias unbr	sudo service unbound restart
alias nsdr	sudo service nsd restart
alias nsdcd	'cd /usr/local/etc/nsd/; ll'
alias vb	sudo $EDITOR /usr/local/etc/unbound/unbound.conf
alias vn	sudo $EDITOR /usr/local/etc/nsd/nsd.conf
alias tlb	tail -n 50 -f /usr/local/etc/unbound/unbound.log
alias tln	tail -n 50 -f /var/log/nsd.log
alias dns	sudo dnstop -l9 lan0
alias dns4	sudo dnstop -4l9 lan0
alias dns6	sudo dnstop -6l9 lan0



#[ ambient and colors ]
umask 22

set path = (/sbin /bin /usr/sbin /usr/bin /usr/local/sbin /usr/local/bin $HOME/bin)

set color

setenv	PAGER	less
setenv	BLOCKSIZE	K

setenv	LSCOLORS	ExGxfxcxBxegehbhbgacad
setenv	LS_COLORS	"di=1;34:ln=1;36:so=35:pi=32:ex=1;31:bd=34;46:cd=34;47:su=31;47:sg=31;46:tw=30;42:ow=30;43"

set	red="%{\033[1;31m%}"
set	green="%{\033[1;32m%}"
set	yellow="%{\033[1;33m%}"
set	blue="%{\033[1;34m%}"
set	magenta="%{\033[1;35m%}"
set	cyan="%{\033[1;36m%}"
set	white="%{\033[1;37m%}"
set	end="%{\033[0m%}"


#[ prompt ]
if ($?prompt) then
	set prompt = "${red}%N@%m:${cyan}%~${white} %#${end} "
#	set prompt = "%N@%m:%~ %# "	# original prompt
	set promptchars = "%#"
	set filec
	set history = 1000
	set savehist = (1000 merge)
	set autolist = ambiguous
	set autoexpand
	set autorehash
	set mail = (/var/mail/$USER)
	if ( $?tcsh ) then
		bindkey "^W" backward-delete-word
		bindkey -k up history-search-backward
		bindkey -k down history-search-forward
	endif
endif

unset red green yellow blue magenta cyan yellow white end
```
>Logo no início do arquivo, altere a linha
>`setenv	EDITOR	ee`
>para definir seu editor preferido como padrão, como nos exemplos abaixo:
>`setenv	EDITOR	vi` ou
>`setenv	EDITOR	nano`

#### 7. Ajustar `rc.conf`
