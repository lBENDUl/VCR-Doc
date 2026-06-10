# Principal

```
sudo su
apt update
parrot-upgrade
```


---
En resumen, primeramente ejecutaremos el siguiente comando:

- **apt install build-essential git vim xcb libxcb-util0-dev libxcb-ewmh-dev libxcb-randr0-dev libxcb-icccm4-dev libxcb-keysyms1-dev libxcb-xinerama0-dev libasound2-dev libxcb-xtest0-dev libxcb-shape0-dev**

Posteriormente, aplicaremos una actualización del sistema con el comando ‘**apt update**‘. Acto seguido, tenéis que dirigiros a la carpeta de descargas de vuestro equipo y descargar los proyectos ‘**bswpm**‘ y ‘**sxhkd**‘ con los siguientes comandos:

- **git clone https://github.com/baskerville/bspwm.git**
- **git clone https://github.com/baskerville/sxhkd.git**

Para instalar cada uno de estos, lo que debéis hacer es meteros en ambos directorios por separado y ejecutar los comandos ‘**make**‘ y ‘**sudo make install**‘.

A continuación tenéis el enlace al archivo de configuración ‘**bspwm_resize**‘ que usamos al final de esta clase:

- [Archivo bspwm_resize](https://hack4u.io/wp-content/uploads/2022/09/bspwm_resize.txt)


**Bspwm (Binary Space Partitioning Window Manager)**

Bspwm es un gestor de ventanas que utiliza la técnica de partición binaria del espacio para organizar las ventanas en el escritorio. Es conocido por su simplicidad y eficiencia, ya que se configura y se controla exclusivamente a través de scripts y comandos en la terminal. Bspwm no maneja teclados ni otros dispositivos de entrada por sí mismo, sino que delega esta tarea a otras herramientas, lo que permite una mayor personalización y flexibilidad.

Cada ventana se organiza automáticamente de manera que ocupe un área divisoria del espacio disponible en el escritorio, optimizando el uso del espacio y facilitando la navegación entre diferentes aplicaciones y documentos abiertos.

**Sxhkd (Simple X Hotkey Daemon)**

Sxhkd es un demonio de teclas de acceso rápido para sistemas X Window. Funciona en conjunto con gestores de ventanas como Bspwm y permite a los usuarios asignar acciones a combinaciones de teclas y botones del mouse. Su configuración se realiza a través de un archivo de texto plano, donde el usuario define las combinaciones de teclas y las acciones correspondientes que se deben ejecutar. Sxhkd es altamente configurable y ligero, diseñado para ser rápido y eficiente en el manejo de eventos de entrada, lo que lo hace ideal para entornos donde los recursos del sistema son limitados o cuando se busca una experiencia de usuario altamente personalizable y controlada.

Ambos programas son muy populares en la comunidad de entusiastas de Linux que prefieren un entorno de escritorio altamente personalizable y orientado al uso de teclado.


Pasos:

```
sudo su

cd /home/bendu/Descargas

apt install build-essential git vim xcb libxcb-util0-dev libxcb-ewmh-dev libxcb-randr0-dev libxcb-icccm4-dev libxcb-keysyms1-dev libxcb-xinerama0-dev libasound2-dev libxcb-xtest0-dev libxcb-shape0-dev

// descargar bspwd de dos maneras:
// manera 1
apt install bspwm

// manera 2
su bendu
git clone https://github.com/baskerville/bspwm.git
cd bspwm/
make
sudo make install

git clone https://github.com/baskerville/sxhkd.git
cd sxhkd
make
sudo  make install

```

Archivos de configuración:
- bspwm -> bspwmrc
- sxhkd -> sxhkdrc

```
mkdir ~/.config/{bspwd,sxhkd}
```

Rutas:

~/Descargas/bspwm/examples/bspwmrc
~/Descargas/bspwm/examples/sxhkdrc

```
cp bspwmrc ~/.config/bspwm/
cp sxhkdrc ~/.config/sxhkd/

cd ~/.config/sxhkd/
```


Instalamos kitty (luego actualizaremos a la versión más nueva)

```
sudo su
apt install kitty
which kitty    // Para ver la ruta y pegarla en el archivo posterior
```



Modificación del archivo de atajos

```
nano sxhkd


# terminal emulator
super + Return
    /usr/bin/kitty

...

#
# bspwm hotkeys
#

# quit/restart bspwm
super + shift + {q,r}
    bspc {quit,wm -r}

# close and kill
super + {_,shift + }q
    bspc node -{c,k}

...

#
# focus/swap
#

# focus the node in the given direction
super + {_,shift + }{Left,Down,Up,Right}
    bspc node -{f,s} {west,south,north,east}

...

#
# preselect
#

# preselect the direction
super + ctrl + shift + alt + {Left,Down,Up,Right}
    bspc node -p {west,south,north,east}

# preselect the ratio
super + ctrl + {1-9}
    bspc node -o 0.{1-9}

# cancel the preselection for the focused node
super + ctrl + alt + space
    bspc node -p cancel

...

#
# move/resize
#

# move a floating window
super + shift + {Left,Down,Up,Right}
    bspc node -v {-20 0,0 20,0 -20,20 0}

#Custom Resize 
super + alt + {Left,Down,Up,Right}
    /home/bendu/.config/bspwm/scripts/bspwm_resize {west,south,north,east}


```


Agregar el script de Resize

```
su bendu
cd /home/bendu/.config/bspwm
mkdir scripts
cd scripts
touch bspwm_resize
chmod +x bspwm_resize
nano bspwm_resize

// Agregamos lo siguiente
#!/usr/bin/env dash

if bspc query -N -n focused.floating > /dev/null; then
	step=20
else
	step=100
fi

case "$1" in
	west) dir=right; falldir=left; x="-$step"; y=0;;
	east) dir=right; falldir=left; x="$step"; y=0;;
	north) dir=top; falldir=bottom; x=0; y="-$step";;
	south) dir=top; falldir=bottom; x=0; y="$step";;
esac

bspc node -z "$dir" "$x" "$y" || bspc node -z "$falldir" "$x" "$y"



chmod +x bspwm_resize
```

---

En esta clase, instalamos Polybar, Picom y Rofi, paquetes esenciales para personalizar tu entorno Bspwm, mejorando la interfaz y la usabilidad.

- **Polybar**: Es una barra de tareas altamente personalizable para sistemas de ventanas X. Polybar se destaca por su flexibilidad y capacidad para mostrar información variada como la fecha, la utilización del CPU, la memoria, y mucho más. Puedes configurar completamente su apariencia y los módulos que muestra, lo que la hace muy popular entre los usuarios que desean un escritorio minimalista y funcional.
- **Picom**: Es un compositor para el sistema de ventanas X, lo que significa que maneja cómo se muestran las ventanas y los efectos visuales en el escritorio, como sombras, transparencias y animaciones suaves. Picom ayuda a mejorar la estética general del escritorio y reduce el desgarro de la pantalla durante la reproducción de video y el movimiento de ventanas.
	https://github.com/yshui/picom

- **Rofi**: Es un lanzador de aplicaciones ligero y personalizable, que también puede servir como conmutador de ventanas y más. Rofi permite a los usuarios buscar y lanzar aplicaciones rápidamente, cambiar entre ventanas activas, o incluso ejecutar comandos personalizados. Su interfaz es altamente configurable, lo que permite a los usuarios adaptarla a sus necesidades específicas y estética del escritorio.

Pasos:

```
sudo su
apt install polybar

apt install libconfig-dev libdbus-1-dev libegl-dev libev-dev libgl-dev libepoxy-dev libpcre2-dev libpixman-1-dev libx11-xcb-dev libxcb1-dev libxcb-composite0-dev libxcb-damage0-dev libxcb-glx0-dev libxcb-image0-dev libxcb-present-dev libxcb-randr0-dev libxcb-render0-dev libxcb-render-util0-dev libxcb-shape0-dev libxcb-util-dev libxcb-xfixes0-dev meson ninja-build uthash-dev cmake

apt update

su bendu
cd ~/Descargas
git clone https://github.com/yshui/picom
cd picom

apt install libconfig-dev

meson setup --buildtype=release build
ninja -C build
ninja -C build install

sudo apt install rofi

cd ~/.config/sxhkd/
nano sxhkdrc


# program launcher
super + d     
    /usr/bin/rofi -show run


```


--- 


Por aquí tienes el archivo ‘**color.ini**‘ para la Kitty:

- [Archivo color.ini](https://hack4u.io/wp-content/uploads/2022/09/color.ini_.txt)

Por aquí tienes el archivo de configuración ‘**kitty.conf**‘ para la Kitty:

- [Archivo kitty.conf](https://hack4u.io/wp-content/uploads/2022/09/kitty.conf_.txt)

Os compartimos a continuación el fondo de pantalla que utilizamos en esta clase:

- [Fondo de pantalla](https://wallpapercave.com/download/4k-ultra-hd-neon-mask-boy-wallpapers-wp7885623)

A continuación, vamos a definir cada uno de los conceptos que tocamos en esta clase:

- **Kitty:** Kitty es un emulador de terminal moderno para sistemas operativos basados en Unix, como Linux y macOS. Es especialmente conocido por su eficiencia y capacidad para manejar gráficos modernos como imágenes y emojis directamente en la terminal. Kitty es altamente personalizable y ofrece funcionalidades avanzadas como pestañas, división de ventanas, y transparencia, entre otros. Utiliza la aceleración por GPU para renderizar los textos, lo que lo hace particularmente rápido.
- **Hack Nerd Fonts:** Es una versión modificada de la fuente mono-espaciada Hack, que ha sido ampliada con una gran cantidad de iconos y símbolos adicionales, conocidos como “glyphs”. Estos incluyen íconos de populares herramientas de desarrollo y sistemas, lo que hace a esta fuente muy útil para desarrolladores y usuarios de terminales, ya que permite visualizar íconos específicos de herramientas directamente en la interfaz de la terminal.
- **Feh:** Feh es un visor de imágenes ligero y rápido para Linux que también puede ser utilizado para configurar fondos de pantalla en sistemas de escritorio. Es muy eficiente y funciona bien en entornos de escritorio ligeros o configuraciones minimalistas, ya que no depende de grandes librerías gráficas. Feh puede ser utilizado en scripts y configuraciones para cambiar automáticamente fondos de pantalla o para mostrar imágenes en presentaciones de diapositivas.

Estas herramientas son bastante populares en la comunidad de usuarios avanzados de Linux, especialmente aquellos que prefieren entornos altamente personalizables y eficientes.


Pasos:

```
cd /.config/sxhkd
nano sxhkdrc


#Open Firefox
super + shift + f
	/usr/bin/firefox


sudo su
cd /usr/local/share/fonts
```

En dicho directorio metemos un archivo de una pagina que descargamos en el firefox
https://www.nerdfonts.com/font-downloads

Y nos descargamos la que se llama 'Hack Nerd Font'

```
mv /home/bendu/Descargas/Hack.zip .
7z x Hack.zip

apt install zsh

```

PARA EL PORTAPAPELES BIDIRECCIONAL

```
su bendu
nano .config/bspwm/bspwmrc

//Añadimos la linea
vmware-user-suid-wrapper &

//Reiniciamos con
win + shift + r

```

ACTULIZACIÓN DE LA KITTY

Buscamos en firefox -> https://github.com/kovidgoyal/kitty, le damos a la derecha donde pone la versión y nos descargamos el fichero llamado -> Linux amd64 binary bundle

```
sudo su
apt remove kitty
cd /opt 
mkdir kitty
cd kitty
mv /home/bendu/Descargas/kitty-0.39.1-x86_64.txz .
7z x kitty-0.39.1-x86_64.txz
rm kitty-0.39.1-x86_64.txz

tar -xf kitty-0.39.1-x86_64.tar 
rm kitty-0.39.1-x86_64.tar 
```

Como ha cambiado el archivo binario de la kitty tenemos que editar el archivo de sxhkdrc y poner la ruta en el atajo

```
nano /home/bendu/.config/sxhkd/sxhkdrc


# terminal emulator
super + Return
    /opt/kitty/bin/kitty


// Tenemos que reiniciar el servicio para que funcione
```

ESTETICA DE LA KITTY

```
cd .config/kitty/
nano kitty.conf




// Agregamos lo siguiente

enable_audio_bell no

include color.ini

font_family HackNerdFont
font_size 13

disable_ligatures never

url_color #61afef

url_style curly

map ctrl+left neighboring_window left
map ctrl+right neighboring_window right
map ctrl+up neighboring_window up
map ctrl+down neighboring_window down

map f1 copy_to_buffer a
map f2 paste_from_buffer a
map f3 copy_to_buffer b
map f4 paste_from_buffer b

cursor_shape beam
cursor_beam_thickness 1.8

mouse_hide_wait 3.0
detect_urls yes

repaint_delay 10
input_delay 3
sync_to_monitor yes

map ctrl+shift+z toggle_layout stack
tab_bar_style powerline

inactive_tab_background #e06c75
active_tab_background #98c379
inactive_tab_foreground #000000
tab_bar_margin_color black

map ctrl+shift+enter new_window_with_cwd
map ctrl+shift+t new_tab_with_cwd

background_opacity 0.95

shell zsh
```

```
Para los colores 
nano color.ini



// Pegamos lo siguiente:
#cursor_shape          Underline
#cursor_underline_thickness 1
window_padding_width  20

# Special
foreground #a9b1d6
background #1a1b26

# Black
color0 #414868
color8 #414868

# Red
color1 #f7768e
color9 #f7768e

# Green
color2  #73daca
color10 #73daca

# Yellow
color3  #e0af68
color11 #e0af68

# Blue
color4  #7aa2f7
color12 #7aa2f7

# Magenta
color5  #bb9af7
color13 #bb9af7

# Cyan
color6  #7dcfff
color14 #7dcfff

# White
color7  #c0caf5
color15 #c0caf5

# Cursor
cursor #c0caf5
cursor_text_color #1a1b26

# Selection highlight
selection_foreground #7aa2f7
selection_background #28344a

```

Cerramos la terminal que tenemos y abrimos otra. Nos pondrá que no tenemos el archivo de la shell por lo que le damos a la opción de `0` para que nos la cree


Para el tratamiento de imágenes

```
sudo apt install imagemagick

// comando con la imagen
kitty +kitten icat gato.jpg
```


Para poder configurar un fondo de pantalla

```
apt instal feh
cd /home/bendu/Desktop
mkdir fondos
mv /home/bendu/Descargas/fondo1.jpg .
nano /home/bendu/.config/bspwm/bspwmrc

// Añadimos la siguiente linea
/usr/bin/feh --bg-fill /home/bendu/Desktop/fondos/fondo1.jpg &

```


Para que root tenga también la kitty

```
sudo su
cd ~/.config/kitty
cp /home/bendu/.config/kitty/* .
/opt/kitty/bin/kitty
```

---

**Polybar** es una herramienta muy utilizada en la personalización de entornos de escritorio en sistemas Linux, especialmente en aquellos que emplean gestores de ventanas livianos o “tiling window managers” como **i3**, **bspwm**, y otros. Es una barra de estado que se destaca por ser altamente configurable y modular.

Polybar permite a los usuarios crear barras de estado que se adaptan precisamente a sus necesidades y estética del escritorio. Puedes configurar elementos como módulos de reloj, indicadores de batería, controles de volumen, monitores de sistema (como CPU, memoria, etc.), espacios de trabajo y muchos otros. Cada módulo en la barra puede ser personalizado en términos de funcionalidad y apariencia.

La configuración de Polybar se realiza a través de archivos de texto plano, lo que proporciona una gran flexibilidad. Los usuarios pueden escribir sus propios módulos usando scripts en diferentes lenguajes de programación o adaptar módulos existentes para personalizar su experiencia. Además, Polybar es capaz de lanzar y mostrar notificaciones o resultados de comandos específicos, lo que lo hace una herramienta extremadamente potente para aquellos que desean tener un control total sobre la información que se muestra en su entorno de escritorio.

En la siguiente clase, trabajaremos en las esquinas, el sombreado y los difuminados.


```
cd Descargas
git clone https://github.com/VaughnValle/blue-sky.git
cd blue-sky/polybar
cp -r * ~/.config/polybar
echo '~/.config/polybar/./launch.sh' >> ~/.config/bspwm/bspwmrc 
sudo su
cp fonts/* isr/share/fonts/truetype/
fc-cache -v
```


---

Por aquí os comparto mi archivo ‘**picom.conf**‘:

- [Archivo picom.conf](https://hack4u.io/wp-content/uploads/2022/09/picom.conf_.txt)

**Picom** es un compositor para sistemas de ventanas X, utilizado comúnmente en entornos de escritorio Linux. Es un derivado de Compton, que a su vez se basó en xcompmgr-dana, y es ampliamente usado en configuraciones con gestores de ventanas que no incluyen su propio sistema de composición, especialmente en entornos minimalistas como i3, bspwm, y otros.

Picom se encarga de añadir efectos visuales que no solo mejoran la estética del escritorio, sino que también pueden ayudar a hacer la interfaz más amigable y menos cansada para la vista.

Entre las funcionalidades que ofrece Picom se encuentran:

- **Sombras**: Añade sombras a las ventanas, lo que ayuda a mejorar la percepción de profundidad en el escritorio. Las sombras pueden ser configuradas en términos de color, tamaño, desenfoque, y opacidad.
- **Bordeados y esquinas redondeadas**: Permite redondear las esquinas de las ventanas, lo que suaviza la apariencia general del entorno de escritorio. También puede gestionar los bordes de las ventanas.
- **Transparencias y difuminados**: Picom puede gestionar la transparencia de las ventanas, los menús y la terminal, permitiendo configurar diferentes niveles de transparencia y efectos de desenfoque. Estos efectos de desenfoque se pueden aplicar a áreas detrás de ventanas transparentes para mejorar la legibilidad y la estética.
- **Animaciones**: Aunque más limitado en comparación con otros compositores como Compiz, Picom también puede manejar algunas animaciones básicas para minimizar y restaurar ventanas.
- **Prevención de tearing**: Picom ayuda a eliminar o reducir el “tearing” de la pantalla, un problema común donde la imagen no se sincroniza correctamente durante el refresco de la pantalla, lo que resulta en una línea horizontal o varias que descomponen la imagen correctamente renderizada.

La configuración de Picom se realiza a través de un archivo de configuración, donde los usuarios pueden detallar sus preferencias para cada uno de estos efectos. Esto lo hace altamente personalizable y muy popular entre los usuarios que buscan mejorar tanto el rendimiento como la apariencia de sus escritorios.

Recordad que este archivo es totalmente personalizable y debéis ajustarlo en base a las capacidades de vuestro equipo o máquina virtual, para evitar que os genere impacto en el rendimiento.


Pasos:

```
cd ~/.config/polybar
mkdir picom
cd picom
nano picom.conf
//Añadimos el contenido y lo guardamos

nano ~/.config/bspwm/bspwmrc
//Añadimos lo siguiente en el fichero
picom &


//Para quitar el borde del focus ponemos lo siguiente
nano .config/bspwm/bspwmrc

//Añadimos la sigueinte linea
bspc config border_width 0
```

--- 

Os compartimos por aquí el código correspondiente al sistema de autocompletado moderno que vemos en el minuto 27:33, el cual podéis introducir en vuestro archivo ‘**.zshrc**‘:

- [https://pastebin.com/H87J3nMj](https://pastebin.com/H87J3nMj)

**ZSH (Z Shell)**

ZSH, o Z Shell, es un intérprete de comandos para sistemas Unix, que se utiliza como una alternativa avanzada al tradicional shell Bash. Ofrece muchas mejoras en términos de características, plugins, y temas, lo que lo hace muy popular entre los usuarios avanzados y desarrolladores.

Algunas de las características más notables de ZSH incluyen:

- **Autocompletado**: ZSH proporciona un sistema de autocompletado potente y versátil que puede predecir comandos basados en el contexto, incluyendo opciones y parámetros.
- **Corrección de errores**: Ofrece sugerencias de corrección cuando se escribe un comando erróneamente.
- **Gestión de scripts**: ZSH es compatible con todos los scripts de Bourne Shell y Bash, y añade sus propias mejoras en la programación de scripts.
- **Personalización**: Se puede personalizar profundamente mediante temas y configuraciones que pueden cambiar su apariencia y comportamiento.

**Powerlevel10k**

Powerlevel10k es un tema para ZSH diseñado para ser visualmente atractivo y altamente informativo. Está diseñado para ser una versión más rápida y personalizable del popular tema Powerlevel9k.

Algunas de las características que lo hacen destacar incluyen:

- **Velocidad**: Es significativamente más rápido que otros temas para ZSH, lo que reduce el tiempo de inicio del terminal y la latencia al escribir comandos.
- **Personalizable**: Viene con un asistente de configuración que guía a los usuarios a través del proceso de configuración, permitiendo personalizar el prompt dependiendo de las preferencias del usuario.
- **Información contextual**: Muestra información útil en el prompt según el contexto, como el estado del repositorio git, el usuario actual, el tiempo de ejecución de comandos, y mucho más.
- **Iconos y fuentes**: Utiliza fuentes de Nerd Fonts para mostrar iconos y otros elementos gráficos que hacen que la información sea fácil de leer y visualmente atractiva.

Juntos, ZSH y Powerlevel10k ofrecen una experiencia de terminal rica y eficiente, mejorando tanto la funcionalidad como la apariencia del entorno de línea de comandos. Estas herramientas son particularmente populares entre los desarrolladores y los entusiastas de la personalización de sistemas Unix/Linux.


Pasos: 

```
sudo su
apt install zsh-autocomplete zsh-autosuggestions zsh-syntax-highlighting

usermod --shell /usr/bin/zsh root
usermod --shell /usr/bin/zsh bendu

su bendu

git clone --depth=1 https://github.com/romkatv/powerlevel10k.git ~/powerlevel10k
echo 'source ~/powerlevel10k/powerlevel10k.zsh-theme' >>~/.zshrc

nano /home/bendu/.zshrc
//Ponemos la ruta absoluta
source /home/bendu/powerlevel10k/powerlevel10k.zsh-theme

zsh
//Seleccionamos lo que queremos y listo


//Para quitarle lo de la derecha y optimizarla
nano .p10k.zsh
// Comentamos todos los elementos de la derecha
// Y agregamos estas lineas
# The list of segments shown on the left. Fill it with the most important segments.
  typeset -g POWERLEVEL9K_LEFT_PROMPT_ELEMENTS=(
    os_icon                 # os identifier
    dir                     # current directory
    vcs                     # git status
    context
    command_execution_time
    status
    # prompt_char           # prompt symbol
  )



sudo su
git clone --depth=1 https://github.com/romkatv/powerlevel10k.git ~/powerlevel10k
echo 'source ~/powerlevel10k/powerlevel10k.zsh-theme' >>~/.zshrc
zsh
//Elegimos otra vez las opciones

cd /root
nano .p10k.zsh
//lo mismo que con el usuario local

nano .10j.zsh
//Fultramos por ROOT_TEMPLATE con cntr + F y borramos el contenido del valor
 typeset -g POWERLEVEL9K_CONTEXT_ROOT_TEMPLATE=''
 typeset -g POWERLEVEL9K_CONTEXT_PREFIX=''

//Aqui ponemos el icono que nosotros queramos
//Para ello vamos a nerdfonts.com/cheat-sheet  (yo busco por 'fire' lo copio y lo pego)
 typeset -g POWERLEVEL9K_CONTEXT_ROOT_TEMPLATE='󰈸'

```


Para quitar la negrita

```
su bendu
nano .p10k.zsh
//Filtrar por DIR_ANCHOR
typeset -g POWERLEVEL9K_DIR_ANCHOR_BOLD=false

sudo su
cd
//Filtrar por DIR_ANCHOR
typeset -g POWERLEVEL9K_DIR_ANCHOR_BOLD=false
```


Centralizar que las shell zsh la tenga como el usuario local como el root

```
sudo su
cd
ln -s -f /home/bendu/.zshrc .zshrc
```


Empezamos con los plugins

```
sudo su
cd /usr/share/zsh-autocomplete
chown root:root /usr/local/share/zsh/site-functions/_bspc

cd
nano .zshrc
//Ingresamos:

# Created by newuser for 5.9
source /home/bendu/powerlevel10k/powerlevel10k.zsh-theme


# ZSH AutoSuggestions Plugin
if [ -f /usr/share/zsh-autosuggestions/zsh-autosuggestions.zsh ]; then
    source /usr/share/zsh-autosuggestions//zsh-autosuggestions.zsh
fi                                                        


# ZSH Syntax Highlighting
if [ -f /usr/share/zsh-syntax-highlighting/zsh-syntax-highlighting.zsh ]; then
    source /usr/share/zsh-syntax-highlighting/zsh-syntax-highlighting.zsh 
fi


# ZSH AutComplete Plugin
if [ -f /usr/share/zsh-autocomplete/zsh-autocomplete.plugin.zsh ]; then
    source /usr/share/zsh-autocomplete/zsh-autocomplete.plugin.zsh 
fi

# To customize prompt, run `p10k configure` or edit ~/.p10k.zsh.
[[ ! -f ~/.p10k.zsh ]] || source ~/.p10k.zsh
source ~/powerlevel10k/powerlevel10k.zsh-theme

```


```
sudo su
cd /usr/share
mkdir zsh-sudo
cd zsh-sudo
wget https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/refs/heads/master/plugins/sudo/sudo.plugin.zsh

/usr/share/zsh-sudo/sudo.plugin.zsh

cd
nano .zshrc

//Escribimos
# ZSH Sudo Plugin
if [ -f /usr/share/zsh-sudo/sudo.plugin.zsh ]; then
    source /usr/share/zsh-sudo/sudo.plugin.zsh
fi

# History
HISTFILE=~/.zsh_history
ISTSIZE=10000
SAVEHIST=10000
```


Para que en los comandos me pueda hacer un autocompletado hacemos lo siguiente

En https://pastebin.com/H87J3nMj copiamos y pegamos en .zshrc


---

Os compartimos por aquí los aliases que tenéis que definir en el archivo ‘**.zshrc**‘, para que los tengáis más accesibles:

- [Archivo .zshrc](https://pastebin.com/QGvVx3wG "Aliases Zshrc")

Por aquí tenéis también la línea del ‘**LS_COLORS**‘ que debéis incluir en el archivo ‘.zshrc’ para evitar que se os vean los nombres de los archivos en negrita:

- [Configuración LS_COLORS](https://pastes.io/xsqk9oi1hz "Configuración LS_COLORS")

Por último, os compartimos el enlace donde se habla sobre cómo solucionar el inconveniente que se presenta a la hora de abrir Burpsuite:

- [Procedimiento para arreglar el inconveniente con Burpsuite](https://medium.com/@neat_mahogany_porcupine_191/burpsuite-no-longer-launches-after-parrot-upgrade-d1c6b17cb70d)
    

**Batcat**

Batcat es la versión distribuida en Debian y Ubuntu del programa **bat**, una herramienta de línea de comandos que sirve como una versión mejorada del clásico comando **cat** de Unix. Batcat ofrece resaltado de sintaxis para una gran variedad de lenguajes de programación y formatos de archivo, facilitando la lectura y comprensión del código directamente en la terminal. Además, integra funcionalidades como la integración con git para mostrar diferencias en el código, numeración de líneas, y la capacidad de previsualizar el contenido de archivos en formato paginado.

Esta herramienta es especialmente útil para desarrolladores y personas que trabajan frecuentemente con código fuente o scripts, ya que mejora significativamente la legibilidad y la eficiencia al explorar o revisar archivos en la terminal.

**Lsd**

Lsd (abreviatura de LSDeluxe) es un reemplazo moderno para el tradicional comando **ls** de Unix, diseñado para mejorar la visualización de los archivos en la terminal. Lsd ofrece un resaltado de colores basado en el tipo de archivo y permisos, además de utilizar iconos para cada tipo de archivo, lo que hace que la navegación y comprensión de la estructura de directorios sea mucho más intuitiva y visualmente agradable. Al igual que bat, lsd soporta fuentes de Nerd Fonts para mostrar iconos, y puede ser completamente personalizado mediante un archivo de configuración para adaptarse a las preferencias del usuario.

Lsd también soporta características como el árbol de directorios directamente en la lista de archivos, lo cual es útil para obtener una visión rápida de la jerarquía de directorios sin necesidad de comandos adicionales. Su funcionalidad y estética mejorada lo hacen popular entre los usuarios que desean una experiencia más rica y eficiente en la línea de comandos.


Pasos:

Descargamos los que sean amd64.deb
https://github.com/sharkdp/bat/releases/tag/v0.25.0
https://github.com/lsd-rs/lsd/releases/tag/v1.1.5


```
sudo su
cd /home/bendu/Descargas
dpkg -i bat_0.25.0_amd64.deb
dpkg -i lsd_1.1.5_amd64.deb

//pruebas
bat /etc/hosts
lsd -l

sudo su
cd
nano .zshrc

//Añadimos la siguiente linea
export PATH=/opt/kitty/bin:/usr/local/bin:/usr/bin:/bin:/usr/local/games:/usr/games:/usr/sbin

//Añadimos tambien lo siguiente
export LS_COLORS="rs=0:di=34:ln=01;36:mh=00:pi=40;33:so=01;35:do=01;35:bd=40;33;01:cd=40;33;01:or=40;31;01:mi=00:su=37>
```


Para los alias ponemos en el fichero .zshrc lo siguiente

```
1. # Custom Aliases
    
2. # -----------------------------------------------
    
3. # bat
    
4. alias cat='bat'
    
5. alias catn='bat --style=plain'
    
6. alias catnp='bat --style=plain --paging=never'
    

7. # ls
    
8. alias ll='lsd -lh --group-dirs=first'
    
9. alias la='lsd -a --group-dirs=first'
    
10. alias l='lsd --group-dirs=first'
    
11. alias lla='lsd -lha --group-dirs=first'
    
12. alias ls='lsd --group-dirs=first'
```

1. Problema con burpsuite (EN MI CASO NO ME DA FALLO)

https://medium.com/@fidjolakoka/burpsuite-no-longer-launches-after-parrot-upgrade-d1c6b17cb70d

1. Desproporción en burpsuite

Pasos:

```
su bendu
cd
nano .zshrc
//Añadimos lo siguiente
#Fix the Java Problem
export _JAVA_AWT_WM_NONREPARENTING=1


nano /home/bendu/.config/bspwm/bspwmrc
//Añadimos lo siguiente
wmname LG3D &

cd /usr/bin
sudo su
touch burpsuite-launcher
chmod +x burpsuite-launcher
nano burpsuite-launcher
//Escribimos lo siguiente
export _JAVA_AWT_WM_NONREPARENTING=1
wmname LG3D &
/usr/bin/burpsuite
```


---

Os compartimos a continuación los scripts correspondientes a los 2 módulos creados para representar la dirección IP de la interfaz ‘**ens33**‘ y la ‘**tun0**‘ correspondiente a la VPN:

- [Archivo ethernet_status.sh](https://pastebin.com/HcKxU3tD)
- [Archivo vpn_status.sh](https://pastebin.com/sUk5hB4Q)

Recordad que en caso de que vuestro nombre de interfaz sea otro, tenéis que adaptarlo en el script. Asimismo, donde pone ‘**ICONO**‘, debéis sustituirlo por vuestro icono deseado de Nerd Fonts.


Pasos:

```
cd .config/polybar
ls

nano launch.sh


nano current.ini
//Filtramos por la palabra log
// Vemos que [bar/log] es el icono del sistema operativo
//Podemos modificarlo como queramos
//El icono lo podemos buscar filtrando por el valor que tiene modules-center
//En nerdfonts.com/cheat-sheet tiene muchos iconos para elegir
//Copiamos el icono donde estaba el otro y ahora tenemos que ver el valor T7
//Para ello filtramos por font- y vemos que no hay font-7
//Añadimos la 7 con lo siguiente:
font-7 = "Hack Nerd Font Mono:size=20;6"
```


Para poner la ip

```
nano launch.sh
// Ponemos lo siguiente para que quede asi
## Left bar
polybar log -c ~/.config/polybar/current.ini &
polybar ethernet_bar -c ~/.config/polybar/current.ini &



// Ahora tenemos que añadir en current.ini el valor ethernet_bar
nano current.ini
// Filtramos por secondary
// Copiamos todo el contenido de [bar/secondary] y pegamos
// Le cambiamos el nombre a [bar/ethernet_bar]
// Tiene que quedar de la siguiente manera
[bar/ethernet_bar]
inherit = bar/main
width = 10%
height = 40
offset-x = 3.8%
offset-y = 15
background = ${color.bg}
foreground = ${color.white}
bottom = false
padding = 1
;padding-top = 2
module-margin-left = 0
module-margin-right = 0
;modules-left = date sep mpd
modules-center = ethernet_status
wm-restack = bspwm




// Filtramos por module/date
// Copiamos la estructura
// Pegamos en algun sitio bueno
//Lo editamos para que quede de la siguiente manera:
[module/ethernet_status]
type = customl/script
interval = 2
exec = ~/.config/scripts/ethernet_status.sh


cd /home/bendu/.config/bspwm/scripts
touch ethernet_status.sh
chmod +x ethernet_status.sh
nano ethernet_status.sh
//El contenido tiene que ser el siguiente
1. #!/bin/sh
    

2. echo "%{F#2495e7}󰈀 %{F#ffffff}$(/usr/sbin/ifconfig ens33 | grep "inet " | awk '{print $2}')%{u-}"

```


Para la vpn

```
//Añadimos en launch.sh para que quede así
## Left bar
polybar log -c ~/.config/polybar/current.ini &
polybar ethernet_bar -c ~/.config/polybar/current.ini &
polybar vpn_bar -c ~/.config/polybar/current.ini &


// En current.ini filtramos por ethernet_bar y copiamos el contenido y lo pegamos
[bar/vpn_bar]
inherit = bar/main
width = 10%
height = 40
offset-x = 14.08%
offset-y = 15
background = ${color.bg}
foreground = ${color.white}
bottom = false
padding = 1
;padding-top = 2
module-margin-left = 0
module-margin-right = 0
;modules-left = date sep mpd
modules-center = vpn_status
wm-restack = bspwm


//Filtramos por module/ethernet_status
//Copiamos y hacemos uno nuevo
[module/vpn_status]
type = custom/script
interval = 2
exec = ~/.config/bspwm/scripts/vpn_status.sh



cd /home/bendu/.config/bspwm/scripts
touch vpn_status.sh
chmod +x vpn_status.sh
nano vpn_status.sh
//El contenido tiene que ser el siguiente
#!/bin/sh

IFACE=$(/usr/sbin/ifconfig | grep tun0 | awk '{print $1}' | tr -d ':')

if [ "$IFACE" = "tun0" ]; then
    echo "%{F#1bbf3e}󰆧 %{F#ffffff}$(/usr/sbin/ifconfig tun0 | grep "inet " | awk '{print $2}')%{u-}"
else
    echo "%{F#1bbf3e}󰆧 %{u-} Disconnected"
fi
```

---


Por aquí os comparto el script ‘**target_to_hack.sh**‘:

- [Archivo target_to_hack.sh](https://pastebin.com/Z0s6zy7W)

Asimismo, os comparto las funciones ‘**settarget**‘ y ‘**cleartarget**‘ las cuales debéis incorporar en tu archivo ‘**.zshrc**‘:

- [Función settarget](https://pastebin.com/z0Hy7PUB)
- [Función cleartarget](https://pastebin.com/JnHSrq3Y)

Recordad que por defecto estas definiciones vienen con la ruta ‘**s4vitar**‘ contemplada, en vuestro caso tendréis que cambiarlo a vuestro usuario correspondiente.

Por otro lado, en el archivo ‘**target_to_hack.sh**‘, cambiad ‘**ICONO**‘ a vuestro icono deseado de Nerd Fonts.


Pasos:

```
//En launch lo siguiente para que quede:
## Right bar
#polybar top -c ~/.config/polybar/current.ini &
polybar target_to_hack -c ~/.config/polybar/current.ini &
polybar primary -c ~/.config/polybar/current.ini &




// En current.ini filtramos por secondary, copiamos el contenido y pegamos uno nuevo
[bar/target_to_hack]
inherit = bar/main 
width = 15%
height = 40 
offset-x = 81.54%
offset-y = 15
background = ${color.bg}
foreground = ${color.white}
bottom = false
padding = 1
;padding-top = 2
module-margin-left = 0
module-margin-right = 0
;modules-left = date sep mpd
modules-center = victim_to_hack
wm-restack = bspwm



//Filtamos por module/ethernet copiamos y pegamos otro nuevo
[module/victim_to_hack]
type = custom/script
interval = 2
exec = ~/.config/bspwm/scripts/victim_to_hack.sh



cd /home/bendu/.config/bspwm/scripts
touch victim_to_hack.sh
chmod +x victim_to_hack.sh
nano victim_to_hack.sh
//El contenido tiene que ser el siguiente
#!/bin/bash

ip_address=$(/bin/cat /home/bendu/.config/bin/target | awk '{print $1}')
machine_name=$(/bin/cat /home/bendu/.config/bin/target | awk '{print $2}')
 
if [ $ip_address ] && [ $machine_name ]; then
    echo "%{F#e51d0b}󰓾 %{F#ffffff}$ip_address%{u-} - $machine_name"
else
    echo "%{F#e51d0b}󰓾 %{u-}%{F#ffffff} No target"
fi



cd /home/bendu/.config/
mkdir bin
cd bin
touch target


nano /home/bendu/.zshrc
//Añadimos lo siguiente
# Custom functions
# ---------------------
# Set Victim Target
function settarget(){
    ip_address=$1
    machine_name=$2  
    echo "$ip_address $machine_name" > /home/bendu/.config/bin/target
}

# Clear Victim Target
function cleartarget(){
    echo '' > /home/bendu/.config/bin/target
}

```


Para que lo del medio de los diferentes entornos se vea diferente

```
nano /home/bendu/.config/polybar/workspace.ini
//Filtramos por occupied y cambiamos los valores y tiene que quedar asi
;label-active = ""
label-active = "∙ "
label-active-foreground = ${color.red}
label-active-background = ${color.bg}

label-occupied = "%icon% "
label-occupied-foreground = ${color.yellow}
label-occupied-background = ${color.bg}
```

Instalar fzf

https://github.com/junegunn/fzf

Pasos:

```
git clone --depth 1 https://github.com/junegunn/fzf.git ~/.fzf
~/.fzf/install
sudo su
git clone --depth 1 https://github.com/junegunn/fzf.git ~/.fzf
~/.fzf/install
```


--- 


Os compartimos a continuación el enlace a la línea que explicamos en el minuto 3:38 para incorporar en los archivos de configuración de Neovim:

- [Línea de configuración para eliminar el $ del final de lína](https://pastebin.com/Lbk9vQLZ)

Asimismo, os explicamos a continuación las diferencias entre Neovim y NVchad:

**Neovim**

Neovim es un editor de texto extensible basado en Vim, diseñado para mejorar la eficiencia en la edición de código y la experiencia del usuario. Se desarrolló como una bifurcación de Vim con el objetivo de modernizar el código base, introducir mejoras en la arquitectura y facilitar la contribución de la comunidad. Neovim añade características como una API mejorada para plugins y soporte integrado para ejecutar procesos de fondo asíncronos, lo que permite una experiencia más fluida y rica en características sin bloquear la interfaz del usuario mientras se ejecutan tareas complejas.

**NVChad**

NVChad es un framework de configuración para Neovim que ofrece una experiencia de usuario mejorada y una suite de características prediseñadas. Es conocido por su interfaz de usuario estéticamente agradable y por su enfoque en la simplicidad y la eficiencia. NVChad viene preconfigurado con una serie de plugins cuidadosamente seleccionados y configuraciones optimizadas que abordan tanto la funcionalidad como la visualización. Esto incluye soporte para temas de color, gestión de buffers mejorada, autocompletado inteligente, y mucho más, todo ello manteniendo un rendimiento rápido. NVChad está diseñado para ser fácil de instalar y configurar, ofreciendo a los usuarios una plataforma robusta sobre la cual pueden construir o adaptar según sus necesidades específicas.

Ambas herramientas están diseñadas para complementarse entre sí, donde Neovim proporciona el entorno base y NVChad aprovecha y amplía sus capacidades, facilitando una experiencia de usuario altamente personalizada y eficiente para programadores y editores de texto.



https://github.com/NvChad/NvChad
https://nvchad.com/docs/quickstart/install/

https://github.com/neovim/neovim/releases/tag/v0.10.4 -> nvim-linux-x86_64.tar.gz 

Pasos:

```
sudo su
apt remove neovim

su bendu
git clone https://github.com/NvChad/starter ~/.config/nvim


cd /opt
sudo su
mkdir nvim
cd nvim
mv /home/bendu/Descargas/nvim-linux-x86_64.tar.gz .
7z l nvim-linux-x86_64.tar.gz
tar -xf nvim-linux-x86_64.tar.gz
rm nvim-linux-x86_64.tar.gz
cd nvim-linux-x86_64/bin 
nano /home/bendu/.zshrc
//En el PATH ponemos la ruta /opt/nvim/nvim-linux-x86_64/bin
//Cierro la consola, abro una nueva y pongo el comando nvim
nvim


su bendu
cd /home/bendu/.config/nvim
nano init.lua
//Añadimos la siguiente linea
vim.opt.listchars = "tab:»·,trail:·"
```

Descargar el comando locate

```
sudo apt install locate
sudo su
updatedb

//Los directorios que pongan acceso denegado ponemos lo siguiente
umount '/run/user/1000/doc'

updatedb

//Nos ponemos en un archivo que sea de lua
nvim init.lua
//En el IDE usamos "Esc" y despues : y ecribimos MasonInstallAll y se instala
//Con "Esc" + Space + t + h puedes cambiar de tema

```


Crear Tema para rofi

```
cd
cd .config
mkdir rofi
cd rofi
mkdir themes
cd themes


cd /opt 
sudo su
git clone https://github.com/newmanls/rofi-themes-collection
cd rofi-themes-collection
cd themes
cp * /home/bendu/.config/rofi/themes/

rofi-theme-selector
//Selecionamos el que queramos y presionamos "Alt" + a
```


```
apt install i3lock
cd /opt 
git clone https://github.com/meskarune/i3lock-fancy.git
cd i3lock-fancy
make install
su bendu
i3lock-fancy


//Para el atajo de teclado
nano nvim /home/bendu/.config/sxhkd/sxhkdrc
// Agregamos lo siguienten
# i3lock-fancy
super + shift + x
	/usr/bin/i3lock-fancy
```

Firefox

En el buscador ponemos
about:config

Y ponemos la siguiente regla:
browser.fixup.domainsuffixwhitelist.htb

Lo ponemos como boolean y le damos al más


Pluggins:
	Wappalyzer -> wappalyzer extension firefox
	Foxyproxy -> Foxyproxy extension firefox



Configuración de burpsuite y foxyproxy

	Accedemos a Proxy -> Proxy settings

En firefox:
	-> Add proxy
		-> Nombre BurpSuite
		-> IP 127.0.0.1
		-> Puerto 8080
save


Certificado BurpSuite
http://burp/ -> CA Certificate

En el navegador vamos a los tres puntos -> Settings -> Buscamos "Cetificates" -> View Certificates -> Autorities -> Import -> Marcar la primera opción


# Atajos de teclado

https://x.com/hack4uacademy/status/1708159121571082707?mx=2

# Mi fondo de pantalla

https://github.com/r1vs3c/auto-bspwm/blob/main/wallpapers/archkali.png

# Quitar lag de teclado VMWARE

https://community.broadcom.com/vmware-cloud-foundation/discussion/ws-1761-keyboard-lag-with-ubuntu-guest

# Ajustar la velocidad del teclado en xset

**Keyboard Rate y Delay**: Ajusta la velocidad del teclado en `xset`:

```
xset r rate 200 50


echo "xset r rate 250 30" >> ~/.xsessionrc

```


# Recursos (Secundario)

## Tmux o Kitty

### Tmux
[[Tmux-Cheat-Sheet.pdf]]

Para colores:
https://github.com/gpakosz/.tmux
```
$ cd
$ git clone --single-branch https://github.com/gpakosz/.tmux.git
$ ln -s -f .tmux/.tmux.conf
$ cp .tmux/.tmux.conf.local .
```

### Kitty

```
curl -L https://sw.kovidgoyal.net/kitty/installer.sh | sh /dev/stdin
```

o si no en la documentación

https://sw.kovidgoyal.net/kitty/binary/


## Instalar NVIM (hay que editarlo) / VScode

Es un editor de texto mejor que el nano

```
sudo apt install neovim
```


## Instalar evilTrust

https://github.com/s4vitar/evilTrust
evilTrust.sh

Te permite obtener la contraseña de una red inalámbrica y redes sociales

[^1]: 
