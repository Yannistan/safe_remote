**Presentation**

The project contains a smart contract which describes a simplified version of trading mechanism, by the aid of an escrow arrangement. In other words, the contract allows for a safe remote purchase system. The contract is deployed in Rinkeby testnet. The owner and escrow addresses are passed as an argument in the constructor. In order that the person that deployes the contract is not necessarily the contract's owner, the contract inherits from an Ownable.sol contract from the OpenZeppelin libraries and makes use of the transferownership() function of the latter contract. 
With the exception of the escrow's address, every other user has to register as either a Seller or a Buyer. If a user has already registered, he is not allowed to re-register with the same Party state. We assume that the registration of each user has a period of 1 year after which the user needs to renew it. If a person is registered as a seller/Buyer, he is also allowed to register as a buyer/seller but retains his id. 
A seller or buyer can credit his account balance via the use of the credit() function.   
A seller has the option to propose an Offer via the order() function. No other parties have access to this function. A buyer (and only this party) performes an order via the order() function and transfers the amount corresponding to the product's price to the escrow account. The latter can only be viewed by the owner. No order can be performed if the buyer does not possess the necessary funds in his account. 
A buyer (and only this Party) has the option to complete, complain about a non-received purchase or even take no action in the latter case, by the aid of the function check_purchase(). When a purchase is completed, the prepaid amount sent in escrow is moved to the seller's account. On the contrary, when the buyer complains about a purchase, the amount is sent back to his balance. Finally, when the buyer does not complain, the prepaid amount remains in the escrow account.


The contract inherits from the Open Zeppelin contract Ownable already tested contracts.
Unit tests are performed by the aid of Mocha test framework, Chai assertion library and Open Zeppelin's Test Environment and Test Helpers.
The contract is deployed via Truffle in Rinkeby Testnet.
Comments are implemented using NatSpec format.

**Installations and Execution**

Install dependencies:
% yarn install

**Install the solc compiler**

As a lot of libraries and tools are configured for using the version 0.6.X we installed the latest 0.6.X version: the version 0.6.12.
We will install solc from the solc-select script, a script to quickly switch between Solidity compiler versions: https://github.com/crytic/solc-select

Macosx
Download and install docker for mac: https://www.docker.com/get-started

When dockeris installed, type the following commands in your termnial:

% cd ~

% docker pull trailofbits/solc-select

% docker run --read-only -i --rm --entrypoint='/bin/sh' trailofbits/solc-select:latest -c 'cat /usr/bin/install.sh' > a.sh

% sed s+\'/usr/bin/solc\'+\'/usr/local/bin/solc\'+ a.sh > b.sh

% chmod a+x b.sh

% ./b.sh

% chmod a+x /usr/local/bin/solc

% rm a.sh b.sh

Linux
% cd ~

% git clone https://github.com/crytic/solc-select.git

% ./solc-select/scripts/install.sh

This will install solc into ~/.solc-select/, so you have to add it to the PATH variable. Add this line, replacing USERNAME with your username, to your ~/.zshrc or equivalent:

export PATH=/home/USERNAME/.solc-select:$PATH
The exact line and the file name should be printed at the end of the installation.

***Usage and configuration***
When installed we can now check all available solc versions:

% solc --versions
and check the current version actually in use:

% solc --version
switch to version 0.6.12:

% solc use 0.6.12
check if current version is 0.6.12:

% solc --version
solc, the solidity compiler commandline interface
Version: 0.6.12+commit.27d51765.Linux.g++
Install solc extension for VSCode
This extension provides syntax highlighting, some error checking and other tools while we are programming in Solidity.

Install the Solidity extension by Juan Blanco: https://marketplace.visualstudio.com/items?itemName=JuanBlanco.solidity

Set the solc version we will use while programming:

RIGHT CLICK on a `.sol` file -> Solidity: Change global compiler version(remote) -> choose 0.6.12


**Install Truffle**

Install truffle globally.

% npm install -g truffle
Check if everything is installed with:

% truffle version
Truffle v5.1.50 (core: 5.1.50)
Solidity v0.5.16 (solc-js)
Node v12.19.0
Web3.js v1.2.9

**Usage**

Created a directory for the project and cd inside.

Initialize a Truffle project, the git repository and a nodejs project:

% truffle init

% git init

% yarn init

Truffle generates build files while compiling your project in the build/ directory.

3 directories and 1 config file are generated by the Truffle project initialization:

contracts/: Directory for solidity contracts
migrations/: Directory for scriptable deployment files
test/: Directory for test files for testing the project and the smart contracts.


***truffle-config.js: Truffle configuration file***

Directory contracts/:
This directory will contain all the solidity files of our project. 

There is also a default Migrations.sol contract in the directory.
This contract keeps track of migrations AKA deployment, this is a configuration contract.

Directory migrations/:

Official documentation on migrations: https://www.trufflesuite.com/docs/truffle/getting-started/running-migrations#migration-files

This directory contains javascript files for managing deployments.

To run all migrations, run the following:

% truffle migrate --network NETWORK_NAME
Networks are configured in truffle-config.js.
If no --network option is provided, the default one will be development, so the command:

% truffle migrate
is an alias for:

% truffle migrate --network development

A development network has to be configured in truffle-config.js.
Often the development network is the Ethereum blockchain running on a ganache node.

Running a migration triggers 2 processes:

compilation: If a solidity file in contracts/ is added or is modified then a compilation by the solc compiler occurs and an artifact for each contract is generated in ./build/contracts.
An artifact is a JSON file containing ABI, byte-code and other information about the contract.
The compiler version and options is defined in the truffle-config.js config file.
The contracts are deployed following the rules described in the javascript files in migrations/ directory.
The available networks for deployment are defined in the truffle-config.js config file.
A default migration script 1_initial_migration.js exists in the migrations/ directory and should never be deleted. 

***Configuration of truffle-config.js***

Official documentation on truffle-config.js: https://www.trufflesuite.com/docs/truffle/reference/configuration
This file is a Javascript file and can execute any code necessary to create our configuration. It must export an object representing your project configuration like the example below:

module.exports = {

  // Configure networks

  networks: {

    development: {

      host: '127.0.0.1',

      port: 8545,

      network_id: '*', // Match any network id

    },

  },

  // Configure MochaJS testing framework

  mocha: {

    // timeout: 100000

  },

  // Configure your compilers

  compilers: {

    solc: {

      version: 'native',

    },

  },

}

Directory test/:

We used the OpenZepplin test helpers and test environment. Install these packages as dev dependencies:

% yarn add --dev @openzeppelin/test-helpers @openzeppelin/test-environment mocha chai

We will not use truffle test for running tests since we switched to OpenZeppelin test env. To this end, we made Mocha the entry point of the test suite by adding to package.json:

"scripts": {

    "test": "npx mocha --exit --recursive"

},

We can now run tests with yarn test command. Smart contracts need to be compiled with truffle compile before running the tests.

To run the tests:

% yarn test

**Infura**

Official website: https://infura.io/

Infura API provides access over HTTPS and WebSockets to the Ethereum and IPFS networks for our applications.
Without Infura we would need to connect directly to an Ethereum node and use the default JSON RPC default API.
Hence, using Infura API we can connect our Frontend, backend and Truffle to the Ethereum network with HTTPS.
In order to deploy on mainnet and testnets Truffle migration scripts need access to the Ethereum network, Infura is an easy way to provide this access over HTTPS/WS.

We need to register on Infura and create an Ethereum project. When a project is created, in the SETTINGS one has access to:

Project ID: Identifier of your project.
Project Secret: The api key of your project, keep it secret.
Endpoints: HTTPS/WS link to access the Infura API for your project.

**Linters and code formatters**

We may need to restart VSCode for applying linters and code formatters.
solidity, eslint and prettier extensions for VSCode are needed before installing the tools below.

***solhint***

solhint: https://protofire.github.io/solhint/
solhint is a linter and perform static analysis of solidity files for finding errors or bad code practice.
Inside the project directory:
We installed solhint as dev dependency:

% yarn add --dev solhint

and created a .solhint.json config file:

{

  "extends": "solhint:recommended",

  "rules": {

    "func-order": "off",

    "mark-callable-contracts": "off",

    "no-empty-blocks": "off",

    "compiler-version": ["error", "^0.6.0"],

    "private-vars-leading-underscore": "error",

    "reason-string": "off"
  }

}

***eslint***

eslint: https://eslint.org/docs/user-guide/getting-started
As our migration and test files are written in JavaScript, we also need a linter for JS files.
eslint is a linter and perform static analysis of solidity files for finding errors and bad code practice.
Inside the project directory:
We installed eslint and its plugins as dev dependency:

% yarn add --dev eslint eslint-config-standard eslint-plugin-import 
eslint-plugin-mocha-no-only eslint-plugin-node eslint-plugin-promise eslint-plugin-standard

and create an .eslintrc config file:

{

  "extends": ["standard", "plugin:promise/recommended"],

  "plugins": ["mocha-no-only", "promise"],

  "env": {

    "browser": true,

    "node": true,

    "mocha": true,

    "jest": true

  },

  "globals": {

    "artifacts": false,

    "contract": false,

    "assert": false,

    "web3": false

  },

  "rules": {

    // Strict mode

    "strict": ["error", "global"],

    // Code style

    "array-bracket-spacing": ["off"],

    "camelcase": ["error", { "properties": "always" }],

    "comma-dangle": ["error", "always-multiline"],

    "comma-spacing": ["error", { "before": false, "after": true }],

    "dot-notation": ["error", { "allowKeywords": true, "allowPattern": "" }],

    "eol-last": ["error", "always"],

    "eqeqeq": ["error", "smart"],

    "generator-star-spacing": ["error", "before"],

    "indent": ["error", 2],

    "linebreak-style": ["error", "unix"],

    "max-len": ["error", 120, 2],

    "no-debugger": "off",

    "no-dupe-args": "error",

    "no-dupe-keys": "error",

    "no-mixed-spaces-and-tabs": ["error", "smart-tabs"],

    "no-redeclare": ["error", { "builtinGlobals": true }],

    "no-trailing-spaces": ["error", { "skipBlankLines": false }],

    "no-undef": "error",

    "no-use-before-define": "off",

    "no-var": "error",

    "object-curly-spacing": ["error", "always"],

    "prefer-const": "error",

    "quotes": ["error", "single"],

    "semi": ["error", "always"],

    "space-before-function-paren": ["error", "always"],

    "mocha-no-only/mocha-no-only": ["error"],

    "promise/always-return": "off",

    "promise/avoid-new": "off"

  },

  "parserOptions": {

    "ecmaVersion": 2018

  }

}

***prettier***

prettieris a code formatter. It formats our code automatically on save.
In the project directory we installed prettier and prettier-plugin-solidity as dev dependency:

% yarn add --dev prettier prettier-plugin-solidity
and created a .prettierrc config file:

{
  "overrides": [

    {

      "files": "*.sol",

      "options": {

        "printWidth": 120,

        "tabWidth": 4,

        "useTabs": false,

        "singleQuote": false,

        "bracketSpacing": false,

        "explicitTypes": "always"

      }

    },

    {

      "files": "*.js",

      "options": {

        "printWidth": 120,

        "tabWidth": 2,

        "useTabs": false,

        "semi": true,

        "singleQuote": true,

        "arrowParens": "always",

        "bracketSpacing": true,

        "trailingComma": "all"

      }

    }

  ]

}

At this point code formatting can be broken.
First try to restart your VSCode. If code formatting still doesn't work right click on an opened solidity file in the editor, select Format Document and select prettier as thedefault formatter. The same procedure applies with a JavaScript file. Now code formatting of JS and solidity file should work.

***EditorConfig***

We installed the EditorConfig extension: https://marketplace.visualstudio.com/items?itemName=EditorConfig.EditorConfig

In the project directory we also created a .editorconfig file:

# EditorConfig is awesome: https://EditorConfig.org

# top-most EditorConfig file
root = true

[*]
charset = utf-8
end_of_line = lf
indent_style = space
insert_final_newline = true
trim_trailing_whitespace = false
max_line_length = 120

[*.sol]
indent_size = 4

[*.js]
indent_size = 2

[*.adoc]
max_line_length = 0


**Compilation and Deployment**

Testnets
There are 4 testnets to deploy the contrat.

Ropsten id: 3
Rinkeby id: 4
Kovan id: 42
Goerli id: 5
We deployed the contract in the network Rinkeby.

***Creating a new account***

To create a new account in teh Ethereum network, we used teh package package mnemonics that generates a mnemonic (a 12 word phrase):

% npx mnemonics

To access our mnemonic as well as our project ID, we use the environmental variables MNEMONIC and ENDPOINT_ID. We define these variables in .env file in the root of our project directory. This file has to be added in .gitignore for security reasons.

We have also installed dotenv which is a module that is used to transmit the environmental variables defined in .env in process.env.

Network Configuration

To sign transaction, we used the HD-WAllet Provider.

% yarn add --dev @truffle/hdwallet-provider

**Deployment in Rinkeby**

% npx truffle console --network rinkeby

Afterwards, we use the javascript web3 functions in order to display a list of teh available accounts:

truffle(rinkeby)> await web3.eth.getAccounts() 
[
     '0x261B63E23eCAAc767Eb5d47F9Ee731651deF9c76', '0x8730d2185956D592d197beEc482b4e6632B6f8f3', '0xdfE3392BC894c22AE894577345D6E8B7E8F54774', '0x8135ef013e6a77Bb434c5e2f69a47796e1206AE1', '0x64d68464971126F06c0F0439E84C6c78c3d37e83', '0xC353733996fd4EfECb07f88a7CFD69c05B7aa416', '0x65BBDf74B9d20B2a3b75e2A2e9643b561b721B8C', '0x6Bc4eBE7096AE84570EF47c6bC389eF8FF452e71', '0x9Eeea75C80AaBfb98b9E97211bc6fF899f6c6Aaa', '0xBD8e7Ad3FC2A6Bfcba664C78DE5DdAFda171e789' 
     ]

The first account, account[0], is used by default by truffle.   After having sent ethers to this account, we deploy the contract by:

% truffle migrate --network rinkeby

contract address : 0x04942bE840A3Cfd0362423CeCbE861C6f9908838
