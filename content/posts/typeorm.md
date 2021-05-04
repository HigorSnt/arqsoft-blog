+++
title = "Documento arquitetural do TypeORM"
date = 2020-10-13
tags = []
categories = []
+++

***

# Autor

Este documento foi produzido por Lucas Henrique de Lima e Silva.

- Matrícula: 115110163
- Contato: lucas.silva@ccc.ufcg.edu.br
- Projeto documentado: https://github.com/typeorm/typeorm

# Descrição Arquitetural -- TypeORM

Este documento descreve parte da arquitetura do projeto TypeORM. Essa descrição foi baseada principalmente no modelo C4.

É importante destacar não será descrita toda a arquitetura do TypeORM, uma biblioteca para projetos em Javascript e Typescript. O foco aqui é a descrição do funcionamento básico da biblioteca através do padrão Active Record, como ela se comunica com os vários tipos de banco de dados e como o programador pode utilizá-la.


## Descrição Geral sobre o TypeORM

TypeORM é uma ferramenta de mapeamento objeto relacional disponível como biblioteca para projetos em Javascript e Typescript. Ela oferece uma interface de programação para gerenciar a persistência em bancos de dados. Esse tipo de ferramenta diminui ou elimina a necessidade de utilizar diretamente comandos SQL, pois é criada uma abstração na própria linguagem para o qual foi construído.

Através deste documento quero descrever a arquitetura da ferramenta e como ela se integra com os bancos de dados e à linguagem Javascript e o superset Typescript. Visto que a ferramenta oferece dois padrões de ORM (Data Mapper e Active Record), estarei utilizando o Active Record como foco. A diferença entre eles pode ser entendida através [deste pequeno post](https://medium.com/@matheusflauzino/entenda-orm-active-record-e-data-mapper-9be60da0e799). Na prática, em TypeORM, o Active Record usa o Data Mapper, expondo os métodos disponíveis (insert, update, delete, count, etc.) diretamente na classe que herda de `BaseEntity`.

## O funcionamento do TypeORM

### Objetivo Geral

Expor uma interface para a manipulação de tabelas em bancos de dados de forma mais fácil e intuitiva, utilizando-se do poder da linguagem javascript e facilitando a construção de sistemas.

### Objetivos Específicos

O objetivo do TypeORM é sempre suportar as últimas funcionalidades de Javascript e prover funcionalidades adicionais para auxiliar no desenvolvimento de qualquer aplicação que usa banco de dados, desde pequenas aplicações com poucas tabelas até aplicações em larga escala com muitos bancos de dados. Além disso, possibilitar a escrita de código de alta qualidade, acoplamento reduzido, com graus elevados de escalabilidade e manutenabilidade de forma mais produtiva também são objetivos da ferramenta.

### Contexto

É fato que a maior parte das aplicações desenvolvidas atualmente se comunicam com bancos de dados. Por muito tempo utilizou-se e até hoje utiliza-se programação direta ou linguagens de consulta de banco de dados para realizar ações simples em um banco de dados, como criar tabelas, inserir, atualizar, deletar valores e executar scripts. No entanto, muitas vezes a camada criada em um software para que ele se comunique com o banco de dados pode ser pouco intuitiva e de difícil manutenção, de acordo com a proficiência da equipe de desenvolvimento as linguagens que manipulam os bancos de dados. Em outros casos, a equipe de desenvolvimento procura organização e boa qualidade de código, sem misturar queries e scripts de banco de dados com o código da aplicação. 

Para mitigar problemas como estes, surgiram as ferramentas chamadas ORM - Mapeamento objeto-relacional (Object-relational mapping em inglês). Hibernate, Doctrine and Entity Framework são exemplos amplamente utilizados. Com ORMs, o programador não precisa se preocupar com os comandos em linguagens de banco de dados, pois basta usar uma interface de programação simples que faz todo o trabalho de persistência.

Javascript e Typescript são altamente poderosos no ramo de desenvolvimento de aplicações, e tomando proveito disso, o TypeORM surgiu. Além de tornar a manipulação e manutenção de banco de dados mais fácil e intuitiva, TypeORM ainda oferece diversas funcionalidades que exploram a fundo o poder dessas linguagens.

O TypeORM funciona em diversos ambientes e se conecta com diversos tipos de banco de dados. Uma aplicação que utiliza o TypeORM pode ser escrita em NodeJS, Browser, Cordova, PhoneGap, Ionic, React Native, NativeScript, Expo e Electron, e pode acessar bancos de dados MySQL, MariaDB, PostgreSQL, CockroachDB, SQLite, Microsoft SQL Server, sql.js e Oracle. Também há suporte experimental para MongoDB.

A arquitetura usada na biblioteca é simples e bem estruturada. O diagrama abaixo mostra como a biblioteca funciona integrada aos bancos de dados e às aplicações que fazem uso deles.

<div align="center" style="margin:2rem 0;">
    <img src="typeorm-contexto.png" style="width:50%;">
    <span style="display:block;font-weight:bold;">
        Figura 1 - Diagrama de contexto do TypeORM
    </span>
</div>

### Containers

Por se tratar de uma biblioteca, o TypeORM é integrado à aplicação que o utiliza, seja no cliente e/ou no servidor. Além disso, ele oferece uma interface de linha de comando, para que o programador possa executar ações de nível de desenvolvimento, como criação de migrações à medida que a aplicação evolui. O banco de dados a ser acessado pode estar totalmente desacoplado da máquina na qual a aplicação roda, pois isso já é previsto pelos protocolos de conexão dos bancos de dados.

A figura abaixo ilustra um exemplo de implantação de um sistema que utiliza TypeORM.

<div align="center" style="margin:2rem 0;">
    <img src="typeorm-containers-implantacao.png" style="width:80%;">
    <span style="display:block;font-weight:bold;">
        Figura 2 - Exemplo de implantação de um sistema utilizando TypeORM
    </span>
</div>

### Componentes

Para se comunicar com o banco de dados, o TypeORM utiliza vários componentes que são interligados e se completam. No momento em que o programador escreve código para consulta ou atualização de valores, o código é transformado em queries de acordo com o tipo de banco de dados que está sendo usado, isso porque cada banco de dados tem sua própria linguagem. 

Para manipular dados, é possível utilizar repositórios (utilizando o padrão Data Mapper) ou Active Records.

Considere o seguinte trecho de código e uma entidade de nome Foo:

`let myFoo = Foo.find({ where: { name: 'Bar' }})`

A figura abaixo mostra como a aplicação se conectará ao banco de dados e como a linha de código acima será transformada em uma consulta no banco de dados PostgreSQL.

<div align="center" style="margin:2rem 0;">
    <img src="typeorm-componentes.png" style="width: 80%;">
    <span style="display:block;font-weight:bold;">
        Figura 3 - Diagrama de componentes do TypeORM 
    </span>
</div>

### Código

<pre>
Nesta etapa não faremos diagramas que apresentam detalhes da
implementação. Faremos isso mais adiante.
</pre>

### Visão de Informação

Através da CLI do TypeORM, é possível gerar migrações. Migrações são necessárias quando a aplicação já está em produção, mas o modelo de dados é modificado ou precisa de alterações não triviais. 

A CLI é capaz de analisar a versão atual do modelo de dados e compará-la com uma versão anterior em busca de mudanças. Caso sejam encontradas mudanças, um script de migração é criado e fica disponível para o TypeORM para ser aplicado manualmente pelo programador ou automaticamente na próxima vez que a aplicação for iniciada.

A figura abaixo ilustra o diagrama de estados de uma migração.

<div align="center" style="margin:2rem 0;">
    <img src="typeorm-informacao.png" style="width: 80%;">
    <span style="display:block;font-weight:bold;">
        Figura 4 - Máquina de estados de uma migração em TypeORM
    </span>
</div>