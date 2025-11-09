# NVX-Bridge-Release

Репозиторий для публикации официальных сборок **NVX-Bridge**. Исходного кода приложения здесь нет: в дереве лежат только лицензии, NOTICE и workflow GitHub Actions, который подтягивает приватный `NVX-Bridge`, собирает исполнимые версии и формирует релиз.

## Что попадает в релизные архивы
- бинарий `NVX-Bridge-<platform>` (или `.exe` для Windows) из `cmd/nvx-bridge`;
- `LICENSE`, `NOTICE` и каталог `licenses/` с текстами сторонних лицензий;
- `REVISION.txt` с укороченным SHA коммита NVX-Bridge, из которого собрана версия.

## Сборочный конвейер (`.github/workflows/build-release.yml`)
1. Репозиторий `NVX-Bridge` клонируется по SSH, фиксируется укороченный SHA (`REVISION.txt` и выход job'а).
2. Для каждой платформы (Linux, macOS, Windows):
   - ставится Go 1.24.9, необходимые GUI зависимости и включается CGO;
   - выполняется `go build` с ключами `-trimpath -ldflags "-s -w"` (Go-linker удаляет таблицу символов и DWARF, т.е. исполнимые файлы уже «stripped»);
   - собирается архив: `tar.gz` для Linux/macOS и `zip` для Windows, рядом пишется SHA256.
3. Job `publish` вытягивает артефакты, формирует тег в формате `vYYYY.MM.DD-<sha>` и создаёт черновик релиза с архивами и файлами контрольных сумм.
