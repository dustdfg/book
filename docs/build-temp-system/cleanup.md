# Очистка и сохранение временной системы

Файлы `libtool` с расширением `.la` могут мешать линковке с динамическими библиотеками. Удалите их:

```bash
find /usr/{lib{,32},libexec} -name \*.la -delete
```

Удалите документацию временных инструментов:

```bash
rm -rf /usr/share/{info,man,doc}/*
```

## Выход из среды chroot

{{ include('../shared/exit-chroot.md') }}

## Удаление отладочных символов

Созданные исполняемые файлы и библиотеки содержат немногим более 90 МБ ненужных отладочных символов.

Удалите символы отладки из двоичных файлов:

```bash
strip --strip-debug $LIN/usr/lib/*
strip --strip-unneeded $LIN/usr/{,s}bin/*
strip --strip-unneeded $LIN/tools/bin/*
```

## Сохранение

При желании сохраните временную систему в архив:

```bash
cd $LIN &&
tar -cJpf $HOME/lin-temp-tools.tar.xz .
```

Это может понадобиться в том случае, если нужно собрать новую систему, либо восстановить уже собранную в случае поломки. Сократит вам время, поскольку потребуется только распаковать временный инструментарий.

## Восстановление

Выполняется из-под хост-системы:

```bash
cd $LIN &&
rm -rf ./* &&
tar -xpf $HOME/lin-temp-tools.tar.xz
```
