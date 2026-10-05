# QR Code Generator

Aplicação Java/Spring Boot para gerar QR Codes a partir de um texto informado e armazená-los em um bucket S3 da AWS.

## Visão geral

Essa API recebe um texto qualquer (URL, token, texto livre etc.), gera uma imagem QR em PNG com a biblioteca ZXing e faz o upload para o S3. Em seguida, retorna a URL pública do arquivo gerado.

## Tecnologias

- Java 21
- Spring Boot 3.4.5
- Maven
- Google ZXing
- AWS SDK v2 para S3

## Estrutura do projeto

```text
src/
├── main/
│   ├── java/
│   │   └── com/devsouzx/qrcode/generator/
│   │       ├── controller/
│   │       ├── dto/
│   │       ├── infraestructure/
│   │       ├── ports/
│   │       └── service/
│   └── resources/
│       └── application.properties
└── test/
```

## Requisitos

- Java 21+
- Maven 3.9+
- Conta AWS com acesso a um bucket S3

## Configuração

Edite o arquivo `src/main/resources/application.properties` com as configurações do seu bucket S3:

```properties
spring.application.name=qrcode.generator
aws.s3.region=us-east-1
aws.s3.bucket-name=seu-bucket
```

Além disso, configure as credenciais AWS no ambiente, usando o mecanismo padrão do AWS SDK (variáveis de ambiente, profile local ou IAM/role da instância):

```bash
export AWS_ACCESS_KEY_ID=seu_access_key
export AWS_SECRET_ACCESS_KEY=sua_secret_key
```

## Execução

### 1. Clonar o projeto

```bash
git clone https://github.com/devsouzx/qrcodegenerator.git
cd qrcodegenerator
```

### 2. Executar a aplicação

```bash
./mvnw spring-boot:run
```

A aplicação estará disponível em:

```text
http://localhost:8080
```

## API

### Endpoint

```http
POST /qrcode
Content-Type: application/json
```

### Exemplo de payload

```json
{
  "text": "https://example.com"
}
```

### Exemplo de resposta

```json
{
  "url": "https://meu-bucket.s3.us-east-1.amazonaws.com/4d2a0a32-8a74-4f15-b619-9a7a1f5c3709"
}
```

## Como funciona

1. A API recebe o texto em JSON.
2. O serviço `QrCodeGeneratorService` cria um QR Code com tamanho 200x200 pixels.
3. A imagem é convertida para bytes PNG.
4. O adaptador `S3StorageAdapter` envia o arquivo para o bucket configurado.
5. A URL pública do objeto no S3 é retornada ao cliente.

## Observações

- A geração do QR Code usa `com.google.zxing`.
- O arquivo gerado recebe um nome UUID aleatório para evitar colisões.
- O endpoint atual retorna um erro 500 em caso de falha durante a geração ou upload.
