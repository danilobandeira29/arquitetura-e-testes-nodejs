# Arquitetura e Testes NodeJS
- **NÃO** existe arquitetura/estrutura perfeita para todos os projetos.
- Cabe a mim, como desenvolvedor, entender o que faz sentido utilizar no meu projeto.
- **GRANDE** parte dos conceitos de escalabilidade irei aprender aqui.
- **Arquitetura não irá importar** se o que eu irei construir será apenas um MVC, onde morrerá logo após a construção.
## **Desenvolver aplicações grande NÃO É SÓ CODAR, é pensar em:**
- **Arquitetura**
- **Testes**
- **Documentação**
- **Organização**
- **Princípios da programação**

Até o momento, o backend está seperando os arquivos por tipo, por exemplo: services, que está lidando com todas as regras de negócio. Dessa forma, a estrutura está desorganizada, caso a estrutura cresça.
<p align="center">
  <img src="https://ik.imagekit.io/xfddek6eqk/Sem_t_tulo_rnRTSVoEF.png" alt="Estrutura de pastas do Backend"/>
  <p align="center">Estrutura de pastas do Backend</p>
</p>

Daqui para frente, serão separados por **Domínio**.
## O que é **Domínio**?
É a área de conhecimento daquele módulo/arquivo.

## Arquitetura baseada em **Domain Driven Design**(DDD)
- É uma metodologia, assim como o **SCRUM**.
- E assim como o **SCRUM**, possuí muitas técnias, mas **nem todas** elas se encaixam em qualquer tipo de projeto.
- Em resumo, **DDD** são **conceitos**, **princípios** e **boas práticas** que devo utilizar em boa partes dos **projetos Backend**.
- **Aplica-se apenas ao Backend**.
## **Test Driven Development** (TDD)
- Também é uma metodologia, assim como o **DDD** e pode ser utilizado em conjunto com o mesmo.
- **Aplica-se no Backend, Frontent e Mobile**.
- **Criar testes, antes de criar as funcionalidades em si.**

## Camada de Módulos/Domínio
- Melhorar a estrutura de pastas da aplicação.
- **Isolar** mais as responsabilidades com base no **domínio**, de qual área de conhecimento um arquivo faz parte.
- Começar a dividir a aplicação em **módulos**.
- **Módulos** são basicamente setores que um ou mais arquivos fazem parte. Responsável pela regra de negócio, é basicamente o coração da minha aplicação.
- Não tem conhecimento
> Exemplo: User, todos os arquivos relacionados a este domínio farão parte do mesmo módulo.

<p align="center">
  <img src="https://ik.imagekit.io/xfddek6eqk/separando_em_modulos_uPmmCtk7X6.png"alt="Camada de módulos/domínio"/>
  <p align="center">Estrutura de pasta da Camada de módulos/domínio</p>
</p>

> Observação: Anteriormente, a entidade **User** fazia parte da pasta **models**, que possuí basicamente o mesmo conceito de **entities**: Como conseguimos representar uma informação na aplicação.

## Shared
- Se mais de um módulo fizer uso de um arquivo, este arquivo deve fazer parte da pasta **Shared**.
> Exemplo: Database, errors, middlewares, routes e etc.
<p align="center">
  <img src="https://ik.imagekit.io/xfddek6eqk/arquivos_de_multiplos_dominios_-MeVKRA9Z.png" alt="Estrutura de pasta shared"/>
  <p align="center">Estrutura de pasta shared</p>
</p>

## Camada de Infra
- **Responsável pela comunicação da minha aplicação com os serviços externos.**
- **Responsável pelas decisões técnicas da aplicação.**
- Em outras palavras, são as ferramentas que serão escolhidas para se **relacionar** com a **camada de módulos**.
> Exemplo: Banco de dados, Serviço de Email automático e por aí vai...
- Na pasta **Shared**, irei adicionar uma pasta **infra** que irá conter todas as informações de um pacote/lib especifico.
- Ainda dentro da pasta **infra**, irei criar uma pasta com arquivos responsáveis pela comunicação com algum protocolo, nesse caso **http**. Ou seja, dentro da **http** terão os arquivos do express ou de qualquer lib que faça uso de protocolos **http**.
<p align="center">
  <img src="https://ik.imagekit.io/xfddek6eqk/camada_de_infra_ZRhR1fyP4.png" alt="Estrutura de pastas da Camada de Infra"/>
  <p align="center">Estrutura de pastas da Camada de Infra</p>
</p>

- Na camada de módulos, **existe a comunicação** entre uma **entidade** e o **banco de dados** que nesse caso é o **TypeOrm**(PostgreSQL). Logo, eu devo criar a pasta **infra/typeorm** que irá conter a pasta de entidade dentro dela.

<p align="center">
  <img src="https://ik.imagekit.io/xfddek6eqk/estrutura_de_pastas_dominio_KeeJeEQj5j.png" alt="Estrutura de pastas da Camada de Domínio com infra"/>
  <p align="center">Estrutura de pastas da Camada de Domínio com a Camada de infra</p>
</p>



