# NVX-Bridge-Release

Этот репозиторий хранит материалы, необходимые для публикации официальных сборок **NVX-Bridge**.
Здесь нет исходного кода приложения — вместо него лежат лицензии, NOTICE и workflow GitHub Actions, который клонирует приватный `NVX-Bridge`, собирает релизы.

## Что попадает в релизные архивы
- бинарий `NVX-Bridge-<platform>` (или `.exe` для Windows), собранный из `cmd/nvx-bridge`;
- `LICENSE`, `NOTICE` и каталог `licenses/` с текстами сторонних лицензий;
- `REVISION.txt` с укороченным SHA коммита NVX-Bridge, из которого собрана сборка.

## Сборочный конвейер (`.github/workflows/build-release.yml`)
1. Репозиторий `NVX-Bridge` клонируется по SSH, фиксируется укороченный SHA (`REVISION.txt` и выход job'а).
2. Для каждой платформы (Linux, macOS, Windows):
   - ставится Go 1.24.9, необходимые GUI зависимости и включается CGO;
   - выполняется `go build` с ключами `-trimpath -ldflags "-s -w"` (Go-linker удаляет таблицу символов и DWARF, т.е. исполнимые файлы уже «stripped»);
   - собирается архив: `tar.gz` для Linux/macOS и `zip` для Windows, рядом пишется SHA256.
3. Job `publish` вытягивает артефакты, формирует тег в формате `vYYYY.MM.DD-<sha>` и создаёт черновик релиза с архивами и файлами контрольных сумм.
