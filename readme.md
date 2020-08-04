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

## **Camada de Módulos/Domínio**
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

## Configurando os Imports
- Ir no tsconfig, no atributo **baseUrl** colocar: 
```
"./src"
```
- E no atributo **paths** e colocar:
```
{ 
  "@modules/*": ["modules/*"],
  "@config/*": ["config/*"],
  "@shared/*": ["shared/*]
}
```
- Logo em seguida, arrumar todas as importações necessarias utilizando **@modules** ou **@config** ou **@shared**
- Corrigir também os imports no arquivo **ormconfig.json**.
- Colocar no **package.json** em **scripts**:
```
{
    "build": "tsc",
    "dev:server": "ts-node-dev -r tsconfig-paths/register --inspect --transpileOnly --ignore-watch node_modules src/shared/infra/http/server.ts",
    "typeorm": "ts-node-dev -r tsconfig-paths/register ./node_modules/typeorm/cli.js"
  },
```
- Instalar para lidar com as importações criadas no arquivo de configuração do TypeScript:
```bash
$ yarn add tsconfig-paths -D
```
- Rodar a aplicação para verificar se está funcionando.

# **SOLID**
## **Liskov Substitution Principle**
- Este princípio define que um **objeto herdado de uma superclass A**, **deve ser possível alterá-lo de tal forma que aceite uma subclasse que também herda a A** sem que a aplicação pare de funcionar.
- Nas camadas(repositories) que temos que dependem de outras bibliotecas(typeorm), devem ser possível alterá-las conforme necessário, seguindo um conjunto de regras. **E no final, iremos depender apenas de um conjunto de regras(interface), mas não necessariamente de uma biblioteca(typeorm) em específico**.

```typescript
// AppointmentsRepository que herda os métodos de um repositório padrão do TypeORM.
import { EntityRepository, Repository } from 'typeorm';
import Appointment from '@modules/appointments/infra/typeorm/entities/Appointment';

@EntityRepository(Appointment)
class AppointmentsRepository extends Repository<Appointment> {
	public async findByDate(date: Date): Promise<Appointment | undefined> {
		const findAppointment = await this.findOne({
			where: { date },
		});
		return findAppointment;
	}
}

export default AppointmentsRepository;

```

### **Aplicando Liskov Substitution Principle**
- Criar uma interface para lidar com o repositório de appointments. Ou seja, estou criando minhas regras.
```typescript
import Appointment from '../infra/typeorm/entities/Appointment';

export default interface IAppointmentsRepository {
  findByDate(date: Date): Promise<Appointment | undefined>;
}

```

- Ir no repositório do Typeorm e adicionar minhas novas regras a classe AppointmensRepository:
```typescript
import { EntityRepository, Repository } from 'typeorm';

import IAppointmentsRepository from '@modules/appointments/repositories/IAppointmentsRepository';

import Appointment from '@modules/appointments/infra/typeorm/entities/Appointment';

@EntityRepository(Appointment)
class AppointmentsRepository extends Repository<Appointment> implements IAppointmentsRepository {
	public async findByDate(date: Date): Promise<Appointment | undefined> {
		const findAppointment = await this.findOne({
			where: { date },
		});
		return findAppointment;
	}
}

export default AppointmentsRepository;


```
> Quando uma classe *extends* outra, significa que ela irá herdar os métodos. Já o *implements* serve como um *shape(formato ou regra)* a ser seguido pela classe, mas **não** que herda seus métodos.

#### **Dessa forma, os nossos services irão depender apenas de regras de repositório, e não necessariamente um repositório do TypeORM ou qualquer que seja a outra biblioteca. Na verdade, o service não deve ter ciência sobre o formato final da estrutura que persiste nossos dados.**

## Reescrevendo Repositórios
- Devo ter mais controle sobre os métodos do repositório, já que eles são herdados do TypeORM, como por exemplo: create, findOne e etc.
- Ir no repositório do TypeORM e fazer a adição do meu próprio método create, que irá usar dois métodos do TypeORM. E também devo criar minha interface ICreateAppointmentDTO.ts:
```typescript
export default interface ICreateAppointmentDTO {
  provider_id: string;
  date: Date;
}

```

```typescript
import { getRepository, Repository } from 'typeorm';

import IAppointmentsRepository from '@modules/appointments/repositories/IAppointmentsRepository';

import ICreateAppointmentDTO from '@modules/appointments/dtos/ICreateAppointmentDTO';

import Appointment from '@modules/appointments/infra/typeorm/entities/Appointment';

class AppointmentsRepository implements IAppointmentsRepository {

  private ormRepository: Repository<Appointment>;

  constructor(){
    this.ormRepository = getRepository(Appointment);
  }


	public async findByDate(date: Date): Promise<Appointment | undefined> {
		const findAppointment = await this.ormRepository.findOne({
			where: { date },
		});
		return findAppointment;
	}

  public async create({ provider_id, date }: ICraeteAppointmentDTO): Promise<Appointment>{
    const appointment = this.ormRepository.create({
      provider_id,
      date
    });

    await this.ormRepository.save(appointment);

    return appointment;
  }
}

export default AppointmentsRepository;

```
## **Dependency Inversion Principle**
Este princípio define que:
- **Módulos de alto-nível não devem depender de módulos de baixo-nível. Ambos devem depender apenas de abstrações(por exemplo, interfaces).**
- **Abstrações não devem depender dos detalhes. Detalhes(implementações) devem depender das abstrações.**

Ou seja, ao invés do meu **service depender diretamente do repositório do typeorm**, **ele agora depende apenas da interface do repositório, que será passado pela rota**.

A rota faz parte da camada de infra, que é a mesma que se encontra o TypeORM, por isso não é necessário aplicar o mesmo princípio a ela. Já o **service faz parte da camada de domínio, logo, deve ser desacoplado da camada de infra** de tal modo que não tenha conhecimento do seu funcionamento.

```typescript
import { startOfHour } from 'date-fns';
import { getCustomRepository } from 'typeorm';
import Appointment from '@modules/appointments/infra/typeorm/entities/Appointment';
import AppError from '@shared/errors/AppError';
import AppointmentsRepository from '../repositories/AppointmentsRepository';

interface Request {
	provider_id: string;
	date: Date;
}

class CreateAppointmentService {
	public async execute({ provider_id, date }: Request): Promise<Appointment> {
    //dependendo do repositorio do typeorm
		const appointmentsRepository = getCustomRepository(AppointmentsRepository);

		const appointmentDate = startOfHour(date);

		const findAppointmentInSameDate = await appointmentsRepository.findByDate(
			appointmentDate,
		);

		if (findAppointmentInSameDate) {
			throw new AppError('This Appointment is already booked!');
		}

		const appointment = await appointmentsRepository.create({
			provider_id,
			date: appointmentDate,
		});

		return appointment;
	}
}

export default CreateAppointmentService;

```

### **Aplicando o Dependency Inversion Principle**
```typescript
import { startOfHour } from 'date-fns';
import Appointment from '@modules/appointments/infra/typeorm/entities/Appointment';
import AppError from '@shared/errors/AppError';
import IAppointmentsRepository from '../repositories/IAppointmentsRepository';

interface IRequest {
	provider_id: string;
	date: Date;
}

class CreateAppointmentService {

  //dependendo apenas da interface(abstração) do repositório, que será passado ao construtor ao ser instanciado um novo repositório na rota.
  constructor(private appointmentsRepository: IAppointmensRepository) {}
  
	public async execute({ provider_id, date }: Request): Promise<Appointment> {
		const appointmentDate = startOfHour(date);

		const findAppointmentInSameDate = await this.appointmentsRepository.findByDate(
			appointmentDate,
		);

		if (findAppointmentInSameDate) {
			throw new AppError('This Appointment is already booked!');
		}

		const appointment = await this.appointmentsRepository.create({
			provider_id,
			date: appointmentDate,
		});

		return appointment;
	}
}

export default CreateAppointmentService;

``` 

> Observação: A entidade Appointment ainda é provida pelo TypeORM, mas nesse caso não será aplicado o Dependecy Inversion Principle para não 'dificultar' o entendimento deste princípio.

### Tanto *Liskov Substitution Principle* quanto *Dependency Inversion Principle* possuem conceitos similares. Resumidamente, o *Liskov Substitution Principle* diz que minha **camada de domínio deve depender de abstrações(interfaces), e não diretamente da camada de infra**. Já o *Dependency Inversion Principle* diz que **módulos devem depender de abstrações(interfaces) e não de outros módulos**.

## Refatorações do Módulo de Usuários
- Criar o repositório de usuários na camada de domínio, que será minha interface que ditará as regras para o repositório do typeorm seguir.
```typescript
// modules/users/repositories/IUsersRepository
import User from '../infra/typeorm/entities/User';
import ICreateUserDTO from '../dtos/ICreateUserDTO';

export default interface IUsersRepository {

	findByEmail(email: string): Promise<User | undefined>;
	findById(id: string): Promise<User | undefined>;
	create(data: ICreateUserDTO): Promise<User>;
	save(user: User): Promise<User>;
}

```

```typescript
// modules/users/dtos/ICreateUserDTO
export default interface ICreateUserDTO {
	name: string;
	email: string;
	password: string;
}

```

- Agora fazer a criação do repositório do typeorm.
```typescript
//modules/users/infra/typeorm/repositories/UsersRepository
import { getRepository, Repository } from 'typeorm';

import User from '../entities/User';
import IUsersRepository from '@modules/users/repositories/IUsersRepository';

class UsersRepository implements IUsersRepository{
	private ormRepository: Repository<User>;

	constructor(){
		this.ormRepository = getRepository(User);
	}

	public async findByEmail(email: string): Promise<User>{
		const user = this.ormRepository.findOne({ where: { email }});

		return user;
	}
	public async findById(id: string): Promise<User>{
		const user = this.ormRepository.findOne(id);

		return user;
	}

	public async create(user: User): Promise<User>{
		const user = this.ormRepository.create(user);

		return this.ormRepository.save(user);
	}

	public async save(user: User): Promise<User>{
		return this.ormRepository.save(user);
	}
}
```

- Agora devo utilizar a interface desse repositório no service, pois em cada service será enviado um repositório, e eu devo informar qual a sua interface.

```typescript
import { hash } from 'bcryptjs';
import AppError from '@shared/errors/AppError';
import User from '../infra/typeorm/entities/User';

import IUsersRepository from '../repositories/IUsersRepository';

interface IRequest {
	name: string;
	email: string;
	password: string;
}

class CreateUserService {
	constructor(private usersRepository: IUsersRepository) {}

	public async execute({ name, email, password }: IRequest): Promise<User> {
		const checkUserExist = await this.usersRepository.findByEmail(email);

		if (checkUserExist) {
			throw new AppError('Email already used!');
		}

		const hashedPassword = await hash(password, 8);

		const user = await this.usersRepository.create({
			name,
			email,
			password: hashedPassword,
		});

		return user;
	}
}

export default CreateUserService;

```
> Fazer essas alterações para os demais services.

- Ir nas rotas do domínio de usuários e fazer a instância do repositório do typeorm que será enviado pelo parâmetro de cada rota.

## **Injeção de Dependência**
- Motivo de utilizar:
	- Não precisar ficar passando os repositórios sempre que um service for instânciado.

- Ir na pasta shared, criar uma pasta container e um index.ts
```typescript
import { container } from 'tsyringe';

import IAppointmentsRepository from '@modules/appointsments/repositories/IAppointmentsRepository';
import IUsersRepository from '@modules/users/repositories/IUsersRepository';

import AppointmentsRepository from '@modules/appointments/infra/typeorm/repositories/AppointmentsRepository';

import UsersRepository from '@modules/users/infra/typeorm/repositories/UsersRepository';

container.registerSingleton<IAppointmentsRepository>('AppointmentsRepository', AppointmentsRepository);

container.registerSingleton<IUsersRepository>('UsersRepository', UsersRepository);

```

- Ir nos services de appointments e fazer as alterações nos constructors de todos os appointments
```typescript
import { startOfHour } from 'date-fns';
import { injectable, inject } from 'tsyringe';

import Appointment from '@modules/appointments/infra/typeorm/entities/Appointment';
import AppError from '@shared/errors/AppError';
import IAppointmentsRepository from '../repositories/IAppointmentsRepository';

interface IRequest {
	provider_id: string;
	date: Date;
}

// adicionar o decorator do tsyringe
@injectable()
class CreateAppointmentService {
	constructor(
		// e irá injetar essa dependência sempre que o service for instânciado.
		@inject('AppointmentsRepository')
		private appointmentsRepository: IAppointmentsRepository,
	) {}

	public async execute({ provider_id, date }: IRequest): Promise<Appointment> {
		const appointmentDate = startOfHour(date);

		const findAppointmentInSameDate = await this.appointmentsRepository.findByDate(
			appointmentDate,
		);

		if (findAppointmentInSameDate) {
			throw new AppError('This Appointment is already booked!');
		}

		const appointment = await this.appointmentsRepository.create({
			provider_id,
			date: appointmentDate,
		});

		return appointment;
	}
}

export default CreateAppointmentService;

```
- Ir nas rotas de appointments, remover o repositório do typeorm, fazer a importação do container do tsyringe e utilizar o método resolve('inserir_o_service_aqui_onde_ele_seria_instanciado).
```typescript
import { Router } from 'express';
import { container } from 'tsyringe';

import CreateAppointmentService from '@modules/appointments/services/CreateAppointmentService';

const appointmentsRouter = Router();

appointmentsRouter.post('/', async (request, response) => {
	const { provider_id, date } = request.body;

	// const appointmentsRepository = new AppointmentsRepository();

	// const createAppointments = new CreateAppointmentService();

	// colocar agora desta forma
	const createAppointments = container.resolve(CreateAppointmentService)
	...


});
```

- Ir no server.ts(arquivo principal) e fazer a importação do container de injeção de dependência.
```typescript
...
import '@shared/container';
...
```

## **Usando Controllers**
- Controllers em uma arquitetura grande possuí pouca responsabilidade, **onde cuidará de abstrair a lógica que estão nas rotas**. Pois já existem os **Services que lidam com as regras de negócio** e o **Repositório que lida com a manipulação/persistência dos dados**.
- As rotas estavam sendo responsáveis apenas por(agora será responsabilidade dos Controllers):
	- Receber os dados da Requisição.
	- Chamar outros arquivos para tratar os dados.
	- Devolver a Resposta.
- Seguindo os padrões de API Restful, os Controllers devem possuir apenas 5 métodos:
	-	Index(listar todos)
	- Show(listar apenas um)
	-	Create(criar)
	- Update(atualizar)
	- Delete(apagar)
> **Caso sejam necessários mais métodos, devo criar um novo controller.**
```typescript
// users/infra/http/controllers/SessionsController.ts
import { Request, Response } from 'express';

import { container } from 'tsyringe';
import AuthenticateUserService from '@modules/users/services/AuthenticateUserService';

class SessionsController {
	public async create(request: Request, response: Response): Promise<Response> {
		const { password, email } = request.body;

		const authenticateUser = container.resolve(AuthenticateUserService);

		const { user, token } = await authenticateUser.execute({ password, email });

		delete user.password;

		return response.json({ user, token });
	}
}

export default SessionsController;

```

```typescript
// sessions.routes.ts
import { Router } from 'express';

import SessionsController from '../controllers/SessionsController';

const sessionsRouter = Router();
const sessionsController = new SessionsController();

sessionsRouter.post('/', sessionsController.create);

export default sessionsRouter;

```

## **Testes e TDD**
Criamos testes para garantir que nossa aplicação continue funcionando corretamente independente do número de funcionalidade e o número de desenvolvedores.
### 3 Principais tipos de testes
1. Teste Unitário
2. Teste de Integração
3. Teste E2E(end-to-end)
## **Teste Unitário**
- Testa funcionalidades específicas da aplicação.
- Necessita que essas funcionalidades sejam **funções puras**.
#### Funções Puras
Não depende de outra parte da aplicação ou de serviço externo.

**Jamais possuirá:**
- Chamadas a API
- Efeitos colaterais
> Exemplo de efeito colateral: Disparar um email sempre que um novo usuário for criado

## **Teste de Integração**
Testa uma funcionalidade completa, passando por várias camadas da aplicação.

Rota -> Controller -> Service -> Repository -> Service -> ...
> Exemplo: Criação de um novo usuário com envio de email.

## **Teste E2E(end-to-end)**
- Simula a ação do usuário dentro da aplicação.
- Utilizado em interfaces(React, React Native).

Exemplo: 
1. Clique no input de email
2. Digite danilobandeira29@gmail.com
3. Clique no input de senha
4. Digite 123456
5. Clique no botão "fazer login"
6. Espera que o usuário seja redirecionado para a Dashboard

## **Test Driven Development(TDD)**
- "Desenvolvimento Dirigido a Testes".
- Criamos alguns testes antes mesmo da criação da funcionalidade em si(ou algumas delas).
> Exemplo: Quando o usuário se cadastrar na aplicação, ele deve receber um email de boas vindas.

## Configurando Jest
```bash
$ yarn add jest -D
$ yarn jest --init
$ yarn add @types/jest ts-jest -D
```
- Configurar o arquivo jest.config.js:
preset: 'ts-jest',
...,
testMatch: [
	'**/*.spec.ts'
]
- Criar um teste apenas para testar as configurações do jest.
```typescript
test('sum two numbers', () => {
	expect(1 + 2).toBe(3);
});

```

## **Pensando em Testes**
- **Devo criar testes para novas funcionalidades e para aquelas que já existem na aplicação.**
- Irei criar testes unitários inicialmente. São testes menores e que **não devem depender de serviços externos**.
- Criando um novo appointment:
	- Preciso receber provider_id e uma data.
	- Esse provider_id deve ser algum que já existe no banco?
	- E se esse provider_id for deletado?
	- Eu devo criar um novo usuário sempre que for executar os testes?
	- Devo criar uma base de dados apenas para os testes?
- **É difícil criar testes que dependem de coisas externas, como Banco de dados, envio de e-mail... etc.**
- **Teste unitário não deve depender de nada além dele mesmo.** Mas no caso dos services, eles dependem de repository que se conecta com o banco. Por isso, irei criar um **fake repository**, onde **não** irá se conectar com o banco de dados. Banco de dados é passivo a erros, por isso evitar se conectar com ele nesse caso.
- **Cada service irá gerar no mínimo um teste unitário.**

## Criando Primeiro Teste Unitário
- Ir na pasta do service do appointment e criar CreateAppointmentService.spec.ts
```typescript
describe('Create Appointment', () => {
	it('should be able to create a new appointment', () => {
	});
});
```
- Irei criar um teste unitário pois é mais simples e **não deve depender de bibliotecas externas**, como o typeorm, algum database e afins.
- Mas para isso, devo criar o meu **fake repository**, que terá a **minha interface de repositório e assim não irá depender do typeorm**, e irá salvar os dados na memória.

### É possível notar o Liskov Substitution e Dependency Inversion Principle aqui. Onde meu service está dependendo apenas de um appointmentsRepository que tenha a tipagem IAppointmentsRepository, independende se é um repositório do typeorm(que salva no banco de dados) ou um repositório que dentro possuí métodos puros do JavaScript(que salva localmente).

```typescript
// @modules/appointments/repositories/fakes/FakeAppointmentsRepository

import { uuid } from 'uuidv4';
import Appointment from '@modules/appointments/infra/typeorm/entities/Appointments';
import ICreateAppointmentDTO from '../dtos/ICreateAppointmentsDTO';
import IAppointmentsRepository from '../IAppointmentsRepository';

class FakeAppointmentsRepository implements IAppointmentsRepository {
	private appointments: Appointments[] = [];

	public async findByDate(date: Date): Promise<Appointment | undefined> {
		const findAppointment = this.appointments.find(
			appointment => appointment.date === date,
		);

		return findAppointment;
	}

	public async create({ provider_id, date }: ICreateAppointmentDTO): Promise<Appointment>{
		const appointment = new Appointment();

		Object.assign(appointment, { id: uuid(), date, provider_id });

		this.appointments.push(appointment);

		return appointment;
	}
}

export default FakeAppointmentsRepository;

```

```typescript
import FakeAppointmentsRepository from '@modules/appoinetments/repositories/fakes/FakeAppointmentsRepository';
import CreateAppointmentService from './CreateAppointmentService';

describe('Create Appointment', () => {
	it('should be able to create a new appointment', () => {
		const fakeAppointmentsRepository = new FakeAppointmentsRepository();
		const createAppointment = new CreateAppointmentService(fakeAppointmentsRepository);

		const appointment = await createAppointment.execute({
			date: new Date(),
			provider_id: '4444'
		})

		expect(appointment).toHaveProperty('id');
		expect(appointment.provider_id).toBe('4444');
	});
});

```

> Ao tentar executar, irá dar error, pois o arquivo de testes não entende a importação @modules

- Ir no jest.config.js e importar:
```javascript
const { pathsToModuleNameMapper } = require('ts-jest/utils');
const { compilerOptions } = require('./tsconfig.json');

module.exports = { 
	moduleNameMapper: pathsToModuleNameMapper(compilerOptions.paths, { prefix: '<rootDir>/src/' })
}

```
- Ir no tsconfig.json e remover todos os comentário e vírgulas desnecessárias.
```bash
$ yarn test
```

## Coverage Report
- Verifica arquivos para saber **quais arquivos já possuem teste**, **os que não estão sendo testados**, **entre outras informações**.
- Para isso, devo ir no *jest.config.js* e habilitar:
```javascript
collectCoverage: true,
collectCoverageFrom: [
	'<rootDir>/src/modules/**/*.ts'
],
coverageDir: 'coverage', // vai ser criado essa pasta na raíz que irá guardar os relatórios
coverageReporters: [
	'text-summary',
	'lcov',
],

```
```bash
$ yarn test

```

- Acessar o html ./coverage/lcov-report/index.html e visualizar o relatório de arquivos que foram cobertos pelos testes.

## Testes de Agendamento
```typescript
import AppError from '@shared/errors/AppError';
import FakeAppointmentsRepository from '../repositories/fakes/FakeAppointmentsRepository';
import CreateAppointmentService from './CreateAppointmentService';

describe('CreateAppointment', () => {
	it('should be able to create a new appointment', async () => {
		const fakeAppointmentsRepository = new FakeAppointmentsRepository();
		const createAppointment = new CreateAppointmentService(
			fakeAppointmentsRepository,
		);

		const appointment = await createAppointment.execute({
			date: new Date(),
			provider_id: '4444',
		});

		expect(appointment).toHaveProperty('id');
		expect(appointment.provider_id).toBe('4444');
	});

	it('should not be able to create two appointments at the same time', async () => {
		const fakeAppointmentsRepository = new FakeAppointmentsRepository();
		const createAppointment = new CreateAppointmentService(
			fakeAppointmentsRepository,
		);
		const date = new Date(2020, 5, 10, 14);

		await createAppointment.execute({
			date,
			provider_id: '4444',
		});

		expect(
			createAppointment.execute({
				date,
				provider_id: '4444',
			}),
		).rejects.toBeInstanceOf(AppError);
	});
});

```

## Testes de Criação de Usuário
- **Os testes unitários, como dito anteriormente, não devem depender de apis ou database, por isso, vou criar meu repositório com Javascript puro para não depender do repositório do typeorm que se comunica com o banco de dados.**
- Criar o repositório fake de users.
```typescript
// @modules/users/repositories/fakes/FakeUsersRepository.ts
import { uuid } from 'uuidv4';

import IUsersRepository from '../IUsersRepository';
import User from '../../infra/typeorm/entities/User';
import ICreateUserDTO from '../../dtos/ICreateUserDTO';

class FakeUsersRepository implements IUsersRepository { 
	private users: User[] = [];

	public async findByEmail(email: string): Promise<User | undefined> {
		const findUser = this.users.find(user => user.email === email);

		return findUser;
	}

	public async findById(id: string): Promise<User | undefined> {
		const findUser = this.users.find(user => user.id === id);

		return findUser;
	}

	public async create(userData: ICreateUserDTO): Promise<User>{
		const user = new User();

		Object.assign(user, { id: uuid() }, userData);

		this.users.push(user);

		return user;
	}

	public async save(user: User): Promise<User>{
		const findIndex = this.users.findIndex(findUser => findUser.id === user.id);

		this.users[findIndex] = user;

		return user;
	}

}

```

- Agora, criar os testes
```typescript
import AppError from '@shared/errors/AppError';
import CreateUserService from './CreateUserService';
import FakeUsersRepository from '../repositories/fakes/FakeUsersRepository';

describe('CreateUser', () => {
	it('should be able to create a new user', () => {
		const fakeUsersRepository = new FakeUsersRepository();
		const createUser = new CreateUserService(fakeUserRepository);

		const user = await createUser.execute({
			name: 'Test example',
			email: 'test@example.com',
			password: '123456'
		});

		expect(user).toHaveProperty('id');
	});

	it('should not be able to create two user with the same email', () => {
		const fakeUsersRepository = new FakeUsersRepository();
		const createUser = new CreateUserService(fakeUserRepository);

		await createUser.execute({
			name: 'Test example',
			email: 'test@example.com',
			password: '123456'
		});

		expect(createUser.execute({
			name: 'Test example',
			email: 'test@example.com',
			password: '123456'
		})).rejects.toBeInstanceOf(AppError);
	});
});

```
```bash
$ yarn test

```

> Caso ocorra erro relacionado ao reflect-metadata, ir jest.config.js e colocar

```javascript
setupFiles: [
		'reflect-metadata'
	],

```

## Testes de Autenticação
- Fazer a criação do AuthenticateUserService.spec.ts
- Para isso, devo fazer a importação de outro service além do AuthenticateUserService, já que para autenticar um usuário, eu preciso criar um usuário.
- Um teste **NUNCA** deve depender de outro.
- Posso abordar de duas maneiras:
	- Utilizar o método *create* do próprio repositório
	- Utilizar o service CreateUserService

```typescript
import CreateUserService from './CreateUserService';
import AuthenticateUserService from './AuthenticateUserService';

describe('AuthenticateUser', () => {
	it('should be able to authenticate', () => {
		const fakeUsersRepository = new FakeUsersRepository();
		const createUser = new CreateUserService(fakeUsersRepository);
		const authenticateUser = new AuthenticateUserService(fakeUsersRepository);

		const user = await createUser.execute({
			name: 'Test unit',
			email: 'testunit@example.com',
			password: '123456',
		})

		const authUser = await authenticateUser.execute({
			email: 'testunit@example.com',
			password: '123456',
		})

		expect(authUser).toHaveProperty('token');
		expect(authUser.user).toEqual(user);

	});
});

```

- Agora, é possível notar que o CreateUserService tem mais que uma responsabilidade:
	- Criação de usuário
	- Gerar hash para a senha do usuário
- **Além de ferir o Single Responsability Principle, está ferindo também o Dependy Inversion Principle**, pois depende **diretamente** do bcryptjs(deveria depender apenas de uma interface) para a lógica de gerar a hash no **próprio** service de criação de usuário e autenticação de usuário.
- Irei aproveitar e isolar o Hash para que seja possível reutilizar em outro service além do CreateUser/AuthenticateUser e assim não ferir o conceito **DRY(Don't Repeat Yourself)**, ou seja, **não repetir regras de negócio**.

```typescript
// CreateUserService
import { hash } from 'bcryptjs';
...

@injectable()
class CreateUserService {
	constructor(
		@inject('UsersRepository')
		private usersRepository: IUsersRepository,
	) {}

	public async execute({ name, email, password }: IRequest): Promise<User> {
		const checkUserExist = await this.usersRepository.findByEmail(email);

		if (checkUserExist) {
			throw new AppError('Email already used!');
		}

		const hashedPassword = await hash(password, 8);

		...
	}
}

export default CreateUserService;

```

```typescript
import { compare } from 'bcryptjs';

...

@injectable()
class AuthenticateUserService {
	constructor(
		@inject('UsersRepository')
		private usersRepository: IUsersRepository,
	) {}

	public async execute({ password, email }: IRequest): Promise<IResponse> {
		const user = await this.usersRepository.findByEmail(email);

		if (!user) {
			throw new AppError('Incorrect email/password combination.', 401);
		}

		const passwordMatched = await compare(
			password,
			user.password,
		);

	...
	}
}

export default AuthenticateUserService;

```



- **Irei criar uma pasta providers que irá conter funcionalidades que serão oferecidas aos services.**
- **Dentro da providers, irei criar HashProvider, que irá conter models(interface), implementations(quais bibliotecas irei utilizar para as hash), fakes(bibliotecas feitas com JS Puro para as hash).**

```typescript
// @modules/users/providers/HashProvider/models
export default interface IHashProvider {
	generateHash(payload: string): Promise<string>;
	compareHash(payload: string, hashed: string): Promise<boolean>;
}

```

```typescript
// @modules/users/provider/HashProvider/implementations
import { hash, compare } from 'bcrypjs';
import IHashProvider from '../models/IHashProvider';

class BCryptHashProvider implements IHashProvider {

	public async generateHash(payload: string): Promise<string>{
		return hash(payload, 8);
	}

	public async compareHash(payload: string, hashed: string): Promise<string>{
		return compare(payload, hashed);
	}

}

export default BCryptHashProvider;

```

- Ir no CreateUserService/AuthenticateUserService e **fazer com que ele dependa apenas da interface do HashProvider**.

```typescript
//CreateUserService
import IHashProvider from '../providers/HashProvider/models/IHashProvider';

@injectable()
class CreateUserService {
	constructor(
		@inject('UsersRepository')
		private usersRepository: IUsersRepository,

		private hashProvider: IHashProvider,
	) {}

	public async execute({ name, email, password }: IRequest): Promise<User> {
		const checkUserExist = await this.usersRepository.findByEmail(email);

		if (checkUserExist) {
			throw new AppError('Email already used!');
		}

		const hashedPassword = await this.hashProvider.generateHash(password);

		const user = await this.usersRepository.create({
			name,
			email,
			password: hashedPassword,
		});

		return user;
	}
}

export default CreateUserService;

```

```typescript
//AuthenticateUserService
import { injectable, inject } from 'tsyringe';
import { sign } from 'jsonwebtoken';
import authConfig from '@config/auth';
import AppError from '@shared/errors/AppError';
import User from '../infra/typeorm/entities/User';

import IUsersRepository from '../repositories/IUsersRepository';
import IHashProvider from '../providers/HashProvider/models/IHashProvider';

interface IRequest {
	email: string;
	password: string;
}

interface IResponse {
	user: User;
	token: string;
}
@injectable()
class AuthenticateUserService {
	constructor(
		@inject('UsersRepository')
		private usersRepository: IUsersRepository,

		private hashProvider: IHashProvider,
	) {}

	public async execute({ password, email }: IRequest): Promise<IResponse> {
		const user = await this.usersRepository.findByEmail(email);

		if (!user) {
			throw new AppError('Incorrect email/password combination.', 401);
		}

		const passwordMatched = await this.hashProvider.compareHash(
			password,
			user.password,
		);

		if (!passwordMatched) {
			throw new AppError('Incorrect email/password combination.', 401);
		}

		const { secret, expiresIn } = authConfig.jwt;

		const token = sign({}, secret, {
			subject: user.id,
			expiresIn,
		});

		return { user, token };
	}
}

export default AuthenticateUserService;

```

- Agora preciso injetar a dependencia do IHashProvider.
```typescript
//@modules/users/provider/index.ts
import { container } from 'tsyringe';

import BCryptHashProvider from './HashProvider/implementations/BCryptHashProvider';
import IHashProvider from './HashProvider/models/IHashProvider';

container.registerSingleTon<IHashProvider>('HashProvider', BCryptHashProvider);

```
- Preciso injetar dessa dependência no service e importar esse index.ts no @shared/container/index.ts;

```typescript
import IHashProvider from '../providers/HashProvider/models/IHashProvider';

@injectable()
class CreateUserService {
	constructor(
		@inject('UsersRepository')
		private usersRepository: IUsersRepository,

		@inject('HashProvider')
		private hashProvider: IHashProvider,
	) {}

	...

	}
}

export default CreateUserService;

```
```typescript
//@shared/container/index.ts
import '@modules/users/providers';

...

```
- Criar meu FakeHashProvider, **pois meus testes não devem depender de estrategias ou outras bibliotecas como bcryptjs**.
```typescript
import IHashProvider from '../models/IHashProvider';

class FakeHashProvider implements IHashProvider {

	public async generateHash(payload: string): Promise<string>{
		return payload;
	}

	public async compareHash(payload: string, hashed: string): Promise<boolean>{
		return payload === hashed;
	}

};

export default FakeHashProvider;

```

- Ir no AuthenticateUserService.spec.ts

```typescript
import CreateUserService from './CreateUserService';
import AuthenticateUserService from './AuthenticateUserService';
import FakeHashProvider from '../providers/HashProvider/fakes/FakeHashProviders';

describe('AuthenticateUser', () => {
	it('should be able to authenticate', () => {
		const fakeUsersRepository = new FakeUsersRepository();
		const fakeHashProvider = new FakeHashProvider();
		const createUser = new CreateUserService(
			fakeUsersRepository,
			fakeHashProvider
		);
		const authenticateUser = new AuthenticateUserService(
			fakeUsersRepository,
			fakeHashProvider
		);

		const user = await createUser.execute({
			name: 'Test unit',
			email: 'testunit@example.com',
			password: '123456',
		})

		const authUser = await authenticateUser.execute({
			email: 'testunit@example.com',
			password: '123456',
		})

		expect(authUser).toHaveProperty('token');
		expect(authUser.user).toEqual(user);

	});
});

```
