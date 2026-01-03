# Установка и настройка Foundry 

* Настроил Wsl и разобрался в навигации в нём.

* в WSL установил Foundry
```bash
# Download foundry installer `foundryup`
curl -L https://foundry.paradigm.xyz | bash
# Install forge, cast, anvil, chisel
foundryup
```

* Создали директорию в которой будет находиться проект :
```bash
mkdir foundry-f23
cd foundry-f23
```

* Далее в папке foundry-f23 создали папку проекта и перешли в неё:

```bash
mkdir foundry-simple-storage-f23
cd foundry-simple-storage-f23
```

* Создали в папке foundry-simple-storage-f23 проект Foundry: 
```bash
forge init
#эта команда предполагает что папка будет пуста , #если в папке имеются файлы то используем команду #forge init --force
```

* Создали имя и адрес электронной почты при помощи команд :
```bash
git config --global user.email "yourEmail@provider.com"
git config --global user.name "yourUsername"
# эти данные отображаются при коммитах
```


* Настроил взаисодействие с репозиторием на GitHub:
1. Создал на GitHub репозиторий

2. Сгенерировал SSH-ключ в wsl:
```bash
ssh-keygen -t ed25519 -C "your_email@example.com"
# Email в кавычках — это комментарий (метка) для идентификации ключа. Он выполняет несколько функций:

# Идентификация владельца: Когда вы смотрите на свой публичный ключ, видите комментарий с email

# Организация: Если у вас несколько ключей (личный, рабочий), можно различать их по email

# Конвенция GitHub: GitHub рекомендует использовать email, привязанный к аккаунту

# Обратная совместимость: Исторически сложилось, что в RSA-ключах часто указывали email
```

3. "Подружил" удалённый репозиторий с локальным
```bash
# Инициализировал git
git init
# Проверил какие удалённые репозитории подключены
git remote -v
# Добавил удалённый репозиторий (origin) с SSH URL
git remote add origin git@github.com:username/your-repo-name.git
#проверил название локальной ветки  
git branch
# запушил на GitHub
git push -u origin main #"-u" флаг которыйсвязывает удалённую ветку origin с локальной main
```

* Создание и конфигурация .env и KeyStore. 

1. В корне проекта создали файл .env(файл для хранения переменных окружения в формате "ключ=значение")

2. Создали keystore
```bash
cast wallet import название_ключа --private-key приватный_ключ --interaactive
#файл сохранится в ~/.foundry/keystores/название_ключа
#так же сразу создаём keystore для сети Sepolia
cast wallet import название_ключа --private-key приватный_ключ_из_MetaMask --interaactive
```

3. Добавили адрес к созданому файлу с ключем (~/.foundry/keystores/название_ключа) в файл .env
```bash
ETH_KEYSTORE=~/.foundry/keystores/название_ключа #ключ из локального блокчейна Anvil
ETH_SEPOLIA_KEYSTORE=~/.foundry/keystores/sepolia #ключ для сестовой сети Sepolia
```

4. Добавили переменные окружения в .env которые будут в себе хранить адрес RPC. 
```bash
ETH_RPC_URL=http://127.0.0.1:8545 # rpc для локального использования через anvil
SEPOLIA_RPC_URL=https://rpc.ankr.com/eth_sepolia/84dcad7548aeda22864a90d8338a9197d47753e4db35a1fa81531ffb2e442872 # rpc для деплоя в тестовую сеть Sepolia. 
```
# Создание смарт-контракта и скрипта для деплоя этого контркта.
1. Написали смарт-контракт SimpleStorage.sol 

2. Написали скрипт DeploySimpleStorage.s.sol Который отвечает за деплой смарт контракта SimpleStorage.sol:
```solidity
// SPDX-License-Identifier: MIT
​
pragma solidity 0.8.19;
​
contract DeploySimpleStorage {
    
}
```
Для того что бы это считалось скриптом Foundry и была возможность получить доступ к расширенной функциональности Foundry нам нужно импортировать Script из "forge-std/Script.sol(forge-std это так называемая Forge Standart Library, представляет набор зарнее написанных контрактов Solidity, предназначеных для упрощения и улучшения скриптов в рамках разработки Foundry)"

Так же что бы исмопртировать ```SimpleStorage``` нам нужно его импортировать ```import {SimpleStorage} from "../src/SimpleStorage.sol";```

```solidity
// SPDX-License-Identifier: MIT
pragma solidity 0.8.19; 
​
import {Script} from "forge-std/Script.sol";
import {SimpleStorage} from "../src/SimpleStorage.sol";
​
contract DeploySimpleStorage is Script {
    
}
```

Каждому скрипту необходима основная функция которая согласно BestPractise называется ```run()```. Когда мы запускаем ```forge script``` именно эта функция запускается
```solidity
// SPDX-License-Identifier: MIT
​
pragma solidity 0.8.19;
​
import {Script} from "forge-std/Script.sol";
import {SimpleStorage} from "../src/SimpleStorage.sol";
​
contract DeploySimpleStorage is Script {
    function run() external returns (SimpleStorage) {
        vm.startBroadcast();
​
        SimpleStorage simpleStorage = new SimpleStorage();
​
        vm.stopBroadcast();
        return simpleStorage;
    }
}
```
В функции `run()` мы испаользуем ключевое слово `vm`, в Foundry это называется чит-кодом поэтому это ключевое слово работает только в Foundry


`vm.startBroadcast` указывает начальную точку для списка транзакций которые будут отправлены на RPC

`vm.stopBroadcast` указывает на конечную точку списка транзакций которые отправляются на URL RPC

# Anliv Запуск и Работа.

1. Запустили Avil
```bash
anvil
```

2. Запускаем скрипт:
```bash
source .env
#загружаем переменные окружения из файла .env
forge script script/DeploySimpleStorage.s.sol --rpc-url $ETH_RPC_URL --keystore $ETH_KEYSTORE --broadcast
```

# Cast. Взаимодействие со смарт контрактами через командную строку и инструмент Cast
## cast send
```solidity
cast send адрес_смарт_контракта_у_которого_вызываем_функцию "store(uint256)" 1337 --rpc-url $RPC_URL --keystore $PRIVATE_KEY
```

## cast call
```bash
# cast call адрес_контракта_у_которого_хотим_вызвать_функцию "сигнатура_функции()"
cast call 0x5FbDB2315678afecb367f032d93F642f64180aa3 "retrieve()"
```

функция вернёт `hex` значение необходимо преобразовать в десятичное число.

```bash
cast --to-base hex_число dec
```

# Деплой смарт контракта в сеть Sepolia

```bash
forge script script/deploySimpleStorage.s.sol --rpc-url $SEPOLIA_RPC_URL --keystore $ETH_SEPOLIA_KEYSTORE --broadcast
```