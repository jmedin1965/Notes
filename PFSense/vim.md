# vim
Created Thursday 23 May 2024

by default vim is not installed. to install

pkg install vim

Once installed there is no alias for vi to launch vim. Need an alias vi=vim

Then, the vim rc files are stuffed. I'm going to copy the ubuntu vimrc files. I've had several goes at trying to fix, but it's too hard. debian.vim is located in $VIMRUNTIME. to get VIMRUNTIME, run the following

``vim -e -T dumb --cmd 'exe "set t_cm=\<C-M>"|echo $VIMRUNTIME|quit' | tr -d '\015``'

	on ubuntu = /usr/share/vim/vim82
	on pfSense = /usr/local/share/vim/vim90


using filer addon to create this file on pfsense. Also copy [/etc/vim/vimrc](file:///etc/vim/vimrc) from ubuntu to [/usr/local/etc/vim/vimrc](file:///usr/local/etc/vim/vimrc) but added the below to the bottom of this file. This is so I cal also have a vimrc.local.

``if filereadable("/usr/local/etc/vim/vimrc.local")``
``    source /usr/local/etc/vim/vimrc.local``
``endif``
 


