{{ include('../packages/sed/README.md') }}

## Настройка

```bash
./configure --prefix=/usr   \
            --host=$LIN_TGT \
            --disable-nls   \
            --bindir=/bin
```

## Сборка

```bash
make
```

## Установка

```bash
make DESTDIR=$LIN install
```
