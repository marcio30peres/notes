# Criando a estrutura do projeto
Todo projeto react tem uma estrutura que pode ser padrão como a que se constrói com o comando 'yarn create react-app' ou uma estrutura construída do zero adicionando cada uma das dependências.

## Estrutura básica

### Criando package.json
Primeiro deve-se criar o arquivo package.json, que guarda informações importantes dentre as quais se destacam as dependências do projeto. Isso se faz com o comando: 
```
yarn init -y
```
### Adicionando dependências react e react-dom
Feito isso se adicionam as primeiras dependências do react. Essa inclusão se faz pelo comando:
```
yarn add react react-dom
```
### Pastas src e public
Em seguida é criada a pasta src. Nela será colocado todo o código fonte da aplicação. Já a pasta public conterá arquivos que serão acessados publicamente, como imagens, index.html, etc. Este último arquivo já pode ser adicionado nesse momento permanecendo apenas com uma div#root onde o conteúdo da página será renderizado e um script importando o arquivo bundle.js que será gerado posteriormente.

## Configurando babel
O babel é uma ferramenta responsável por converter o código react em um código que o browser entenda e possa servir ao cliente.

### Adicionando dependências
A dependência babel-loader fará a integração do babel com o webpack. A @babel/core contém o coração da biblioteca e seu funcionamento. A @babel/cli permite a execução do babel no terminal. Já a @babel/preset-env identifica o ambiente em que a aplicação será executada para que o código seja otimizado para esse meio. Por fim, a @babel/preset-react permite que o babel compreenda código html dentro dos arquivos .jsx

Para adicioná-las basta rodar o comando:
```
yarn add babel-loader @babel/core @babel/cli @babel/preset-env @babel/preset-react -D
```

### Arquivo babel.config.js
Este arquivo criado na raíz do projeto guarda as configurações do projeto. Inicialmente seu conteúdo especifica aprenas os pressets env e react instalados na etapa anterior.

```js
module.exports = {
    presets: [
        '@babel/preset-env',
        ['@babel/preset-react', {
            //não é necessário fazer a importação do react em todas as páginas
            runtime: 'automatic'
        }]
    ]
}
```

Para exportar os arquivos do projeto usando o babel basta rodar o comando:

```
yarn babel src/index.jsx -o dist/bundle.js
```
OBS.: 
* É convencionado utilizar o nome bundle para arquivos gerados com o babel;
* O parâmetro -o indica a especificação do arquivo de saída;
* A extensão do arquivo index pode variar, mas o arquivo bundle é sempre .js

## Configurando webpack
O webpack é uma ferramenta que conjuntamente com o babel faz a conversão de diversas extensões de arquivos como sass, js, hbs, png, etc... para entensões estáticas entendidas pelo browser como css, js e jpg.

### Instalando dependências
Para instalar o webpack é muito simples. Basta adicionar as dependências de desenvolvimento webpack, webpack-cli, webpack-dev-server e html-webpack-plugin. A webpack contém toda a biblioteca do webpack. A webpack-cli permite a execução no terminal. A webpack-dev-server permite o acompanhamento das mudanças na aplicação sem precisar executar o comando 'yarn webpack'. Já a html-webpack-plugin importa o script de forma automática na página index com algumas poucas configurações. Para iniciar a instalação basta executar o seguinte comando:

```
yarn add webpack webpack-cli html-webpack-plugin -D
```

### Arquivo webpack.config.js
Esse arquivo também criado na raíz do projeto contém as principais configurações do webpack. Sua estrutura básica é similar ao do babel.config.js, iniciando com:

```js
module.exports = {
    
}
```

OBS.: Também é importante indicar uma boa prática. Para evitar erros nos caminhos dos arquivos lidos em diferentes sistemas operacionais, utiliza-se um path que faz a conversão apropriada. basta importá-lo no início do arquivo:

```js
const path = require('path')

//exemplo de utilização
path.resolve(__dirname, 'src', 'index.jsx')
```

Voltando à construção das configurações do webpack, segue-se adicionando o modo de execução do webpack que pode ser development ou production.

```js
const path = require('path')

module.exports = {
    mode: 'development',
}
```

Depois, adicionam-se os source maps, que facilitam a visualização de erros no log do navegador habilitando a visualização dos arquivos da aplicação como estão no editor vs-code. 

```js
const path = require('path')

module.exports = {
    mode: 'development',
    devtool: 'eval-source-map'
}
```

Feito isso, especifica-se qual o arquivo de entrada da aplicação através do parâmetro entry:

```js
const path = require('path')

module.exports = {
    mode: 'development',
    devtool: 'eval-source-map',
    entry: path.resolve(__dirname, 'src', 'index.jsx'),
}
```

Em seguida, expecifica-se o arquivo de saída que será gerado pelo webpack. No caso, bundle.js:

```js
const path = require('path')

module.exports = {
    mode: 'development',
    devtool: 'eval-source-map',
    entry: path.resolve(__dirname, 'src', 'index.jsx'),
    output: {
        path: path.resolve(__dirname, 'dist'),
        filename: 'bundle.js'
    }
}
```

Feito isso, adiciona-se os parâmetros resolve e module, que especificam os tipos de arquivos suportados e o tratamento dado pelo webpack a cada extensão:

```js
const path = require('path')

module.exports = {
    mode: 'development',
    devtool: 'eval-source-map',
    entry: path.resolve(__dirname, 'src', 'index.jsx'),
    output: {
        path: path.resolve(__dirname, 'dist'),
        filename: 'bundle.js'
    },
    resolve: {
        extensions: ['.js', '.jsx']
    },
    module: {
        rules: [
            {
                //testa se a extensão do arquivo é .jsx
                test: /\.jsx$/,
                //isenta a responsabilidade de tratar arquivos de bibliotecas adicionadas
                exclude: /node_modules/,
                //utiliza o babel para tratar os arquivos
                use: 'babel-loader'
            }
        ]
    }
}
```

Como próximo passo, basta importar e configurar o html-webpack-plugin. A importação é feita no início do arquivo e sua confuguração se faz através do parâmetro plugins:

```js
const path = require('path')
const HtmlWebpackPlugin = require('html-webpack-plugin')

module.exports = {
    mode: 'development',
    devtool: 'eval-source-map',
    entry: path.resolve(__dirname, 'src', 'index.jsx'),
    output: {
        path: path.resolve(__dirname, 'dist'),
        filename: 'bundle.js'
    },
    resolve: {
        extensions: ['.js', '.jsx']
    },
    module: {
        rules: [
            {
                test: /\.jsx$/,
                exclude: /node_modules/,
                use: 'babel-loader'
            }
        ]
    },
    plugins: [
        //recebe como parâmetro o objeto cuja propriedade template referencia
        //o arquivo no qual o bundle.js será importado
        new HtmlWebpackPlugin({
            template: path.resolve(__dirname, 'public', 'index.html')
        })
    ]
}
```

Configura-se, então, o webpack-dev-server através do parâmetro devServer, indicando o diretório do arquivo de conteúdo estático index.html, a pasta public:

```js
const path = require('path')
const HtmlWebpackPlugin = require('html-webpack-plugin')

module.exports = {
    mode: 'development',
    devtool: 'eval-source-map',
    entry: path.resolve(__dirname, 'src', 'index.jsx'),
    output: {
        path: path.resolve(__dirname, 'dist'),
        filename: 'bundle.js'
    },
    resolve: {
        extensions: ['.js', '.jsx']
    },
    module: {
        rules: [
            {
                test: /\.jsx$/,
                exclude: /node_modules/,
                use: 'babel-loader'
            }
        ]
    },
    plugins: [
        new HtmlWebpackPlugin({
            template: path.resolve(__dirname, 'public', 'index.html')
        })
    ],
    devServer: {
        contentBase: path.resolve(__dirname, 'public')
    }
}
```

Também é possível habilitar a importação e leitura de arquivos .scss diretamente no jsx. Basta instalar as dependências style-loader, css-loader, sass-loader e node-sass através do comando:

```
yarn add style-loader css-loader sass-loader node-sass -D
```

Por fim adicionam-se as regras para .scss no parâmetro module:

```js
const path = require('path')
const HtmlWebpackPlugin = require('html-webpack-plugin')

module.exports = {
    mode: 'development',
    devtool: 'eval-source-map',
    entry: path.resolve(__dirname, 'src', 'index.jsx'),
    output: {
        path: path.resolve(__dirname, 'dist'),
        filename: 'bundle.js'
    },
    resolve: {
        extensions: ['.js', '.jsx']
    },
    module: {
        rules: [
            {
                test: /\.jsx$/,
                exclude: /node_modules/,
                use: 'babel-loader'
            },
            {
                test: /\.scss$/,
                exclude: /node_modules/,
                use: ['style-loader', 'css-loader', 'sass-loader']
            }
        ]
    },
    plugins: [
        new HtmlWebpackPlugin({
            template: path.resolve(__dirname, 'public', 'index.html')
        })
    ],
    devServer: {
        contentBase: path.resolve(__dirname, 'public')
    }
}
```

Com essas configurações o webpack já pode ser utilizado, sendo sua execução iniciada através do comando:

```
yarn webpack serve
```

O acesso à aplicação poder ser feito pelo navegador na porta indicada na saída do comando anterior.

## Separando ambiente de desenvolvimento e produção
Essa diferenciação é importante pois a aplicação deve ter um comportamento durante seu desenvolvimento e outro quando servida como produto final. A identificação do estado em que a mesma se encontra se dá pela variável de ambiente NODE_ENV.

### Instalando dependências
É preciso instalar a dependência cross-env. Esta biblioteca é responsável por criar as variáveis de ambiente independente do sistema operacional. Mac e Linux tem formas nativas de fazê-lo, porém o Windows não. Esta solução se aplica a todos eles. A instalação se faz pelo comando:

```
yarn add cross-env -D
```

### Scripts no arquivo package.json
Neste arquivo é possível adicionar uma sessão de scripts que funcionam como atalhos para execução de comandos muito longos ou que dependem de estados de variáveis como é o caso da NODE_ENV. Isso se faz através do parâmetro scripts:

```js
"scripts": {
    //NODE_ENV permanece nula (desenvolvimento)
    "dev": "webpack serve",
    //NODE_ENV recebe o valor producion (produção)
    "build": "cross-env NODE_ENV=production webpack"
}
```

### Atualizando arquivo webpack.config.json
Agora que os valores de NODE_ENV já estão definidos para os atalhos dev e build basta capturar o valor da variável no arquivo webpack.config.json e fazer as condicionais nos parâmetros desejados:

```js
const isDevelopment = process.env.NODE_ENV !== 'production'

module.exports = {
    mode: isDevelopment ? 'development' : 'production',
    devtool: isDevelopment ? 'eval-source-map' : 'source-map',
...
```

Agora a estrutura da aplicação está devidamente construída e pronta para a criação do primeiro componente react!