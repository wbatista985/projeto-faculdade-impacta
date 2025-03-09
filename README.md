   # Projeto desenvolvido em Angular e Java <img src="https://raw.githubusercontent.com/devicons/devicon/master/icons/java/java-original.svg" alt="java" width="40" height="40"/> </a>
<div style="display: inline_block">
Realizando um projeto de cadastro de funcionários e conectando o Backend ao Frontend  <img  align="center" alt="html5" src="https://img.shields.io/static/v1?label=Cadastro&message=Funcionarios&color=blueviolet"/>  <a href="https://www.java.com" target="_blank"> 
  
  #### Desenvolvedores
- [Wagner dos Santos ](https://github.com/wbatista985)

#### Projeto Faculdade Impacta
- [ Faculdade Impacta ](https://www.faculdadeimpacta.com/pt-br/)
 
## Requisitos

Para construir e executar a aplicação, você precisa de:

- [Spring Boot](http://maven.apache.org/download.cgi)
- [Maven](http://maven.apache.org/download.cgi)
- [PostgresSQL](http://maven.apache.org/download.cgi)
- [JPA](http://maven.apache.org/download.cgi)
- [Spring Framework](http://maven.apache.org/download.cgi)

# `Backend`
   
## `Running`

Primeiro, clone o projeto e faça o build localmente:

```shell
https://github.com/wbatista985/projeto-faculdade-impacta.git
```

Certifique-se de ter um banco de dados PostgreSQL chamado "postgres".

A partir do diretório raiz do projeto, execute:

```shell
spring-boot: run
```
### Classe modelo Funcionário
Temos uma API escrita em Java, utilizando o framework Spring, que é usada para cadastrar funcionários. Para que nossa aplicação funcione, é necessário que os funcionários sejam cadastrados no sistema. Cada funcionário é representado pela seguinte classe:
```
  
@Entity
@Table(name = "tab_funcionario")
public class Funcionario {
	@Id
	@GeneratedValue(strategy = GenerationType.IDENTITY)
	private Integer id;

	@Column(nullable = false, length = 50)
	private String nome;
	
	@Column()
	private String email;

	@Column(name = "cpf_cnpj", nullable = false, length = 20)
	private String cpfCnpj;

	@Enumerated(EnumType.STRING)
	@Column(name = "sx", columnDefinition = "char(1)")
	private Sexo sexo;

	@Embedded
	private Endereco endereco;

	@ManyToOne()
	@JoinColumn(name = "prof_id")
	private Profissao profissao;
	
	@ElementCollection
	@CollectionTable(name = "telefones")
	private Set<String> telefones = new HashSet<String>();

  
```

- `@Entity`: Nossa classe Funcionario é uma entidade que será mapeada para nosso banco de dados. Usamos a anotação `@ElementCollection` para declarar um mapeamento de coleção de elementos. Todos os registros da coleção são armazenados em uma tabela separada. A configuração dessa tabela é especificada usando a anotação `@CollectionTable`.
A anotação `@CollectionTable` é usada para especificar o nome da tabela que armazena todos os registros da coleção 


- `@Id/@GeneratedValue`: O atributo anotado será a chave primária da tabela e será gerado automaticamente usando a estratégia `IDENTITY`.

   
- `@ElementCollection`: Use a anotação `@ElementCollection` para declarar um mapeamento de coleção de elementos. Todos os registros na coleção são armazenados em uma tabela separada. A configuração para esta tabela é especificada usando a anotação `@CollectionTable`.
  Pode ser usado para definir um relacionamento `one-to-many` para um objeto Embeddable ou um valor Basic (como uma coleção de Strings). Um ElementCollection também pode ser usado em combinação com um Map para definir relacionamentos onde a chave pode ser qualquer tipo de objeto e o valor é um objeto Embeddable ou um Basic.


- `@CollectionTable`: A anotação `@CollectionTable` é usada para especificar o nome da tabela que armazena todos os registros na coleção.
### Temos nossa classe FuncionarioController 
   
```   

@RestController
@RequestMapping("/funcionarios")
@CrossOrigin
public class FuncionarioController {
	@Autowired
	private FuncionarioService funcionarioService;

	@GetMapping(path = "/listar")
	public ResponseEntity<List<Funcionario>> listar() {
		List<Funcionario> funcionarios = funcionarioService.listarFuncionarios();
		return ResponseEntity.ok().body(funcionarios);
	}

	@PostMapping(path = "/inserir")
	public ResponseEntity<Funcionario> inserir(@RequestBody FuncionarioDTO funcionarioDto) {
		Funcionario funcionario = funcionarioService.fromDTO(funcionarioDto);
		Funcionario novoFuncionario = funcionarioService.inserirFuncionario(funcionario);
		return ResponseEntity.ok().body(novoFuncionario);
	}

	@PutMapping(path = "/atualizar")
	public ResponseEntity<Funcionario> atualizar(
			@RequestBody FuncionarioDTO funcionarioDto) {
		Funcionario funcionario = funcionarioService.fromDTO(funcionarioDto);
		funcionarioService.atualizarFuncionario(funcionario);
		Funcionario funcionarioAtualizado = funcionarioService.buscarFuncionario(funcionario);
		return ResponseEntity.ok().body(funcionarioAtualizado);
	}

	@DeleteMapping(path= "/deletar/{id}")
	public ResponseEntity<Void> deletar(@PathVariable Integer id) {
		funcionarioService.deletarFuncionario(id);
		return ResponseEntity.noContent().build();
	}
}

   
   
```
- `@RestController`: Indica que este controlador responderá em formato JSON por padrão.  
   
- `@RequestMapping`: Utilizamos ele para mapear as urls dos nossos métodos, neste caso, todos os métodos deste controlador serão baseados em “/cadastros”

- `@Autowired`: Indica que os parâmetros do construtor serão injetados automaticamente.

- `@GetMapping`: Mapeia requisições HTTP GET para métodos específicos.

- `@PostMapping`: Mapeia requisições HTTP POST para salvar dados.

- `@PutMapping`: Mapeia requisições HTTP PUT para atualizar dados.

- `@DeleteMapping`: Mapeia requisições HTTP DELETE para exclusão de registros.

- `@RequestBody`: Essa anotação é usada para indicar que o objeto FuncionarioDTO deve ser extraído do corpo da requisição HTTP. Quando um cliente envia uma requisição POST ou PUT com um JSON no corpo, o Spring converte automaticamente esse JSON para um objeto Java correspondente, desde que os tipos sejam compatíveis.

- `@PathVariable`: Essa anotação é usada para capturar valores dinâmicos da URL e passá-los como parâmetros para um método do controlador. Geralmente é utilizada quando um endpoint precisa lidar com identificadores específicos, como IDs.

- `@CrossOrigin`: Essa anotação é usada para habilitar o CORS nele (por padrão, permite todas as origens e os métodos HTTP especificados na anotação `@RequestMapping` tenha acesso para realizar as chamadas.

- `@Service`: A anotação @Service é usada para marcar uma classe como um componente de serviço no Spring. Isso significa que o framework reconhecerá essa classe como um bean gerenciado, permitindo que ela seja injetada em outras partes do sistema usando injeção de dependência.

   
### Para acessar nosso banco de dados, temos a classe FuncionarioRepository:
   
  
   
```
   public interface FuncionarioRepository extends JpaRepository<Funcionario, Integer> {

   }
   
```
Ele faz com que o framework veja nossa classe e indicamos que ela é um repositório, ou seja, uma classe cuja única função é acessar o banco de dados,  herdando métodos do `JpaRepository`.


### A classe de serviço `FuncionarioService` e ver como ela realiza a tarefa de salvar um novo usuário: 
```
   
   @Service
public class FuncionarioService {
	@Autowired
	private FuncionarioRepository funcionarioRepository;

	public List<Funcionario> listarFuncionarios() {
		return funcionarioRepository.findAll();
	}

	public Funcionario buscarFuncionarioPorId(Integer id) {
		Optional<Funcionario> funcionarioExistente = funcionarioRepository.findById(id);

		return funcionarioExistente.orElseThrow(() -> new ObjectNotFoundException(
		"Funcionario não encontrado! Id: " + id + ", Tipo: " + Funcionario.class.getName()));
	}

	public Funcionario buscarFuncionario(Funcionario funcionario) {
		Optional<Funcionario> funcionarioExistente = funcionarioRepository.findById(funcionario.getId());

		return funcionarioExistente.orElseThrow(() -> new ObjectNotFoundException(
		"Funcionario não encontrado! Id: " + funcionario.getId() + ", Tipo: " + Funcionario.class.getName()));
	}

	public Funcionario inserirFuncionario(Funcionario funcionario) {
		funcionario.setId(null);
		return funcionarioRepository.save(funcionario);
	}

	public void atualizarFuncionario(Funcionario funcionario) {
		Funcionario funcionarioExiste = buscarFuncionario(funcionario);

		if (funcionarioExiste != null) {
			funcionarioRepository.save(funcionario);
		}
	}

	public void deletarFuncionario(Integer id) {
		Funcionario funcionarioExiste = buscarFuncionarioPorId(id);
		if (funcionarioExiste != null) {
			funcionarioRepository.delete(funcionarioExiste);
		}
	}

	public Funcionario fromDTO(FuncionarioDTO objDto) {
		Funcionario funcionario = new Funcionario(objDto.getId(), objDto.getNome(), objDto.getEmail(),
				objDto.getCpfCnpj(), objDto.getSexo(), objDto.fromEndereco(objDto.getEndereco()),
				objDto.fromProfissao(objDto.getProfissao())

		);
		Set<String> telefones = new HashSet<String>();
		telefones.add(objDto.getTelefone1());
		if (objDto.getTelefone2() != null) {
			telefones.add(objDto.getTelefone2());
		}
		if (objDto.getTelefone3() != null) {
			telefones.add(objDto.getTelefone3());
		}
		funcionario.setTelefones(telefones);
		return funcionario;
	}

}

   
```

### Data Transfer Object – DTO

Um DTO (Data Transfer Object) é um objeto de transferência de dados usado para transportar informações entre diferentes camadas da aplicação. Ele é uma alternativa ao uso direto das entidades do banco de dados, ajudando a manter a separação de responsabilidades e melhorando a segurança e eficiência da aplicação.

Esse padrão também é usado para evitar expor a camada de persistência e garantir que os dados sejam transferidos corretamente para a apresentação.

Por exemplo, ao listar 10 registros, a persistência os busca como entidades (POs). Como frameworks como JPA e Hibernate usam lazy loading, alguns dados podem não estar disponíveis após o fechamento da conexão. Para evitar erros, a aplicação converte essas entidades em DTOs, garantindo que todos os dados necessários estejam carregados e possam ser usados na apresentação sem depender da conexão com o banco.

- [Link exemplo dto project ](https://www.devmedia.com.br/diferenca-entre-os-patterns-po-pojo-bo-dto-e-vo/28162#:~:text=Data%20Transfer%20Object%20%E2%80%93%20DTO&text=Esse%20padr%C3%A3o%20tamb%C3%A9m%20%C3%A9%20bastante,pessoas%20cadastradas%20em%20uma%20tabela.)
 
# Frontend
	
<img  align="center" alt="" src="https://i.imgur.com/MtH61PG.png"/>
</div><br/>

Esse projeto foi gerado com [Angular CLI](https://github.com/angular/angular-cli) version 12.0.5.

## Development server

Run `ng serve` para subir a aplicação. Navegue até `http://localhost:4200/`. O aplicativo será recarregado automaticamente se você alterar qualquer um dos arquivos de origem.

## Code generate

Run `ng generate component component-name` é gerado um novo componente. também pode usar `ng generate directive|pipe|service|class|guard|interface|enum|module`.

## Build

Run `ng build` to build the project. The build artifacts will be stored in the `dist/` directory.

## Running unit tests

Run `ng test` to execute the unit tests via [Karma](https://karma-runner.github.io).

## Running end-to-end tests

Run `ng e2e` to execute the end-to-end tests via a platform of your choice. To use this command, you need to first add a package that implements end-to-end testing capabilities.

## Further help

To get more help on the Angular CLI use `ng help` or go check out the [Angular CLI Overview and Command Reference](https://angular.io/cli) page.
