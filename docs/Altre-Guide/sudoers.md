La funzione di `sudoers.d/nomeutente` su ArchLinux consente agli utenti con privilegi di amministratore di specificare in modo più dettagliato quale utente può eseguire specifici comandi tramite Sudo. 

Di seguito sono elencati alcuni comandi esistenti in questo formato: 

- `nomeutente ALL=(ALL) ALL` - permette all'utente "nomeutente" di eseguire qualsiasi comando come amministratore 
- `nomeutente ALL=(root) /usr/bin/pacaur` - consente all'utente "nomeutente" di eseguire solo il comando "pacaur" come l'utente "root" 
- `nomeutente ALL=(user) /bin/vim ` - consente all'utente "nomeutente" di eseguire solo il comando "vim" come "user"
- `nomeutente ALL=(ALL) NOPASSWD: /usr/bin/pacman -Syu` - consente all'utente "nomeutente" di eseguire "pacman -Syu" come amministratore senza dover inserire la password
- `nomeutente ALL=(ALL) NOPASSWD: ALL` - consente all'utente "nomeutente" di eseguire qualsiasi comando come amministratore senza dover inserire la password.
- `nomeutente ALL=(ALL) /bin/reboot, /bin/shutdown` - permette all'utente "nomeutente" di eseguire solo i comandi "reboot" e "shutdown" come amministratore.
- `%gruppo ALL=(ALL) /usr/bin/htop` - consente a tutti gli utenti appartenenti al gruppo "gruppo" di eseguire il comando "htop" come amministratore.
- `nomeutente ALL=(ALL) !/usr/bin/rm` - permette all'utente "nomeutente" di eseguire qualsiasi comando come amministratore, eccetto il comando "rm".
- `nomeutente ALL=(ALL) NOPASSWD: /bin/su - user` - consente all'utente "nomeutente" di eseguire il comando "su" per diventare l'utente "user" come amministratore senza dover inserire la password.

Nota che questi sono solo alcuni esempi di ciò che può essere specificato nel file `sudoers.d/nomeutente`.
