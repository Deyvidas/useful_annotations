- [Установка интерпретатора Python](#1)
- [Установка менеджера пакетов Pip](#2)
- [Установка менеджера пакетов Poetry](#3)
- [Экспортировать зависимости Poetry в requirements.txt](#4)
- [Импортировать зависимости из requirements.txt в Poetry](#5)

<!-- @@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@ -->

---

<h3 id="1" align="center">Установка интерпретатора Python</h3>

- [Python Developer's Guide](https://devguide.python.org/getting-started/setup-building/)
- [Posit Documentation](https://docs.posit.co/resources/install-python-source/)
- cкрипт можно запускать из любой директории
- после себя удаляет все побочные директории, скачанные в процессе установки
- устанавливает интерпретатор в `/usr/lib/python{PYTHON_VERSION}`
- бинарник кладет в `/usr/bin -> /usr/bin/python{PYTHON_VERSION}`

<details><br><summary><b>Код скрипта</b></summary>

```shell
INITIAL_PATH=${PWD}
PYTHON_MAJOR=3

# Ask Python version.
echo See available versions of Python on website https://www.python.org/ftp/python/
echo And enter python version with format 0.0.0
read -p '>>> ' PYTHON_VERSION
PYTHON_ROOT=/usr/lib/python${PYTHON_VERSION}

# Ask if User want install Pip manager.
while true; do
    echo "Do you want to install Pip manager? (y|n)"
    read -p '>>> ' INSTALL_PIP

    if [[ ${INSTALL_PIP} == 'y' ]]; then
        INSTALL_PIP=--with-ensurepip=upgrade
        break

    elif [[ ${INSTALL_PIP} == 'n' ]]; then
        INSTALL_PIP=--with-ensurepip=no
        break
    fi
done

# Ask number of cores want use User for installation.
echo Enter number of cores which you want to use for installation
read -p '>>> ' CORES

# Update list of packages, update all packages and remove unused dependencies.
sudo apt update && sudo apt -y full-upgrade && sudo apt -y autoremove
# Install required packages to install Python.
sudo apt -y install curl make tar wget
# Install packages required for Python configuration.
sudo apt -y install build-essential \
                    gdb \
                    lcov pkg-config \
                    libbz2-dev \
                    libffi-dev \
                    libgdbm-dev \
                    libgdbm-compat-dev \
                    liblzma-dev \
                    libncurses5-dev \
                    libreadline6-dev \
                    libsqlite3-dev \
                    libssl-dev \
                    lzma \
                    lzma-dev \
                    tk-dev \
                    uuid-dev \
                    zlib1g-dev

# Download directory with Python source code.
sudo wget https://www.python.org/ftp/python/${PYTHON_VERSION}/Python-${PYTHON_VERSION}.tgz
# Unarchive archived directory and go inside them.
sudo tar -zxvf Python-${PYTHON_VERSION}.tgz && cd Python-${PYTHON_VERSION}

# Configure installation of Python.
# --with-ensurepip=no (don`t install pip package).
# --with-ensurepip=upgrade (install last version of pip package).
sudo ./configure ${INSTALL_PIP} \
                 --enable-optimizations \
                 --enable-shared \
                 --prefix=${PYTHON_ROOT} \
                 LDFLAGS=-Wl,-rpath=${PYTHON_ROOT}/lib,--disable-new-dtags

# Generate Makefile for Python installation.
# -s (with only warnings and errors)
# -j<num> (adjust the number of used cores)
sudo sudo make -s -j${CORES}
# Run Python installation.
sudo make install

# Copy executable file in /usr/bin directory
sudo cp ${PYTHON_ROOT}/bin/python${PYTHON_MAJOR} /usr/bin/python${PYTHON_VERSION}

# Delete directories which was needed to install Python but not required for Python work.
cd ${INITIAL_PATH}
sudo rm -r Python-${PYTHON_VERSION}.tgz Python-${PYTHON_VERSION}

# Check version of installed Python.
PYTHON_BIN_PATH=`which python${PYTHON_VERSION}`
echo python successfully installed and executable bath is ${PYTHON_BIN_PATH}
echo "${PYTHON_BIN_PATH} --version ~~> `${PYTHON_BIN_PATH} -V`"
```

</details>

<!-- @@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@ -->

---

<h3 id="2" align="center">Установка менеджера пакетов Pip</h3>

- [pip documentation](https://pip.pypa.io/en/stable/installation/)

```bash
# Install pip if in configuration --with-ensurepip=no
python${PYTHON_VERSION} -m ensurepip -U && python${PYTHON_VERSION} -m pip install -U pip
```

<!-- @@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@ -->

---

<h3 id="3" align="center">Установка менеджера пакетов Poetry</h3>

- [poetry-documentation](https://python-poetry.org/docs/#installing-with-the-official-installer)
- установит основную директорию poetry в `/usr/lib/poetry`
- положит бинарник в `/usr/bin -> /usr/bin/poetry`
- создаст файл `~/.bash_completion` необходимый для работы автодополнения по нажатию на `<TAB>`

<details><br><summary><b>Код скрипта</b></summary>

```shell
# Directory when poetry put dir with program.
POETRY_HOME=/usr/lib/poetry

# Install poetry into directory /usr/lib/poetry.
sudo curl -sSL https://install.python-poetry.org | sudo POETRY_HOME=${POETRY_HOME} python3

# Directory with poetry bin file.
POETRY_BIN=${POETRY_HOME}/bin

# Move binary file of poetry to /usr/bin.
sudo cp ${POETRY_BIN}/poetry /usr/bin
sudo rm -r ${POETRY_BIN}

# Check if poetry was successfully installed.
echo "poetry --version ~~> `poetry --version`"

# Enable tab completion for Bash.
poetry completions bash >> ~/.bash_completion
echo
echo For enable autocompletion restart your shell.

# Directory when poetry put dir with program.
POETRY_HOME=/usr/lib/poetry

# Install poetry into directory /usr/lib/poetry.
sudo curl -sSL https://install.python-poetry.org | sudo POETRY_HOME=${POETRY_HOME} python3

# Directory with poetry bin file.
POETRY_BIN=${POETRY_HOME}/bin

# Move binary file of poetry to /usr/bin.
sudo cp ${POETRY_BIN}/poetry /usr/bin
sudo rm -r ${POETRY_BIN}

# Check if poetry was successfully installed.
echo "poetry --version ~~> `poetry --version`"

# Enable tab completion for Bash.
poetry completions bash >> ~/.bash_completion
echo
echo For enable autocompletion restart your shell.

# ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
# Make settings for poetry.
# ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
# The path to the cache directory used by Poetry.
poetry config cache-dir "~/poetry_cache"
# ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
# https://python-poetry.org/docs/configuration/#installermax-workers
poetry config installer.max-workers 4
# ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
# Use a more modern and faster method for package installation.
poetry config installer.modern-installation true
# ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
# Use parallel packages installation.
poetry config installer.parallel true
# ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
# Create a new virtual environment if one doesn’t already exist.
poetry config virtualenvs.create true
# ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
# Create the virtualenv inside the project’s root directory.
poetry config virtualenvs.in-project true
# ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
# https://python-poetry.org/docs/configuration/#virtualenvsoptionsalways-copy
poetry config virtualenvs.options.always-copy false
# ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
# Create virtualenv without pip package.
poetry config virtualenvs.options.no-pip true
# ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
# Create virtualenv without setuptools package.
poetry config virtualenvs.options.no-setuptools true
# ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
# https://python-poetry.org/docs/configuration/#virtualenvsoptionssystem-site-packages
poetry config virtualenvs.options.system-site-packages false
# ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
# Directory where virtual environments will be created if virtualenvs.in-project false.
poetry config virtualenvs.path {cache-dir}/virtualenvs
# ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
# https://python-poetry.org/docs/configuration/#virtualenvsprefer-active-python-experimental
poetry config virtualenvs.prefer-active-python false
# ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
# Format string defining the prompt to be displayed when the virtual environment is activated.
poetry config virtualenvs.prompt {project_name}
# ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
```

</details>

<!-- @@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@ -->

---

<h3 id="4" align="center">Экспортировать зависимости Poetry в requirements.txt</h3>

```bash
# Экспортировать (ВСЕ) зависимости в requirements.txt.
poetry show | awk '{ print $1"=="$2 }' FS=' ' > requirements.txt

# Экспортировать (ВСЕ) зависимости (ИСКЛЮЧАЯ group1 и group2) в requirements.txt.
poetry export --without group1,group2 --without-hashes --without-urls | awk '{ print $1 }' FS=' ; ' > requirements.txt

# Экспортировать (ОСНОВНЫЕ) зависимости в requirements.txt.
poetry export --without-hashes --without-urls | awk '{ print $1 }' FS=' ; ' > requirements.txt

# Экспортировать (ОСНОВНЫЕ) зависимости (ВКЛЮЧАЯ group1 и group2) в requirements.txt.
poetry export --with group1,group2 --without-hashes --without-urls | awk '{ print $1 }' FS=' ; ' > requirements.txt
```

</details>

<!-- @@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@ -->

---

<h3 id="5" align="center">Импортировать зависимости из requirements.txt в Poetry</h3>

```bash
poetry add $(<requirements.txt)
```