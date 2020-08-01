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