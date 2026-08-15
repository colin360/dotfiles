{{ if not (.chezmoi.kernel.osrelease | lower | contains "microsoft") -}}
#!/usr/bin/env bash

ver=''   # eg: v3.5.0

asset='JetBrainsMono.tar.xz'
root='https://github.com/ryanoasis/nerd-fonts'

if [ -z "$ver" ]; then
    url="${root}/releases/latest/download/${asset}"
else
    url="${root}/releases/download/${ver}/${asset}"
fi

echo Downloading nerd font $asset from $url

mkdir -p ~/.local/share/fonts/JetBrainsMonoNerdFont
rm -f ~/.local/share/fonts/JetBrainsMonoNerdFont/*
curl -fsSL $url | tar -xJf - -C ~/.local/share/fonts/JetBrainsMonoNerdFont

fc-cache -f
{{ end -}}
