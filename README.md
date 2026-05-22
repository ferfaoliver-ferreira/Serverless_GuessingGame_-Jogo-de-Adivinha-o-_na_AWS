# 🎮 Serverless Guessing Game (Jogo de Adivinhação) na AWS

Este repositório contém a documentação técnica e o guia de implementação de uma aplicação web inteiramente serverless construída na Amazon Web Services (AWS). O objetivo do projeto é demonstrar na prática como construir, integrar, testar e desestruturar uma arquitetura desacoplada, escalável e de alta disponibilidade, eliminando completamente a sobrecarga de gerenciamento de servidores.

---

## 🏗️ Arquitetura da Solução

A aplicação foi desenhada seguindo o modelo de microsserviços e computação orientada a eventos (*Event-Driven Architecture*), dividida em três camadas independentes:

1. **Camada de Apresentação (Frontend):** Uma interface estática (HTML5, CSS3 e JavaScript) hospedada e distribuída de forma pública e global através do **Amazon S3**.
2. **Camada de Integração e Roteamento (API):** O **Amazon API Gateway (HTTP API)** atua como a porta de entrada única, gerenciando as requisições assíncronas do cliente, tratando políticas de segurança de CORS (*Cross-Origin Resource Sharing*) e encaminhando o tráfego de forma otimizada.
3. **Camada de Computação (Backend):** Uma função serverless no **AWS Lambda** desenvolvida em **Python 3.13**, responsável por executar a lógica de negócio (validação dos palpites com base em um número secreto aleatório).

---

## 🛠️ Tecnologias e Serviços AWS Utilizados

* **AWS Lambda:** Computação serverless executada sob demanda.
* **Amazon API Gateway:** Exposição e gerenciamento de endpoints RESTful com baixo acoplamento.
* **Amazon S3 (Simple Storage Service):** Armazenamento de objetos e hospedagem estável de sites estáticos.
* **Python 3.13:** Engine do backend para processamento lógico rápido.
* **JavaScript (Fetch API):** Consumo assíncrono de APIs no lado do cliente.

---

## 🚀 Guia de Implementação Passo a Passo

### Etapa 1: Criação da Função AWS Lambda
O ponto de partida do backend consistiu em instanciar um ambiente de computação isolado.
1. No painel do AWS Lambda, criamos uma função configurada do zero.
2. Definiu-se o nome do recurso como `LambdaGame-FernandaOliveira`.
3. O ambiente de execução selecionado foi o **Python 3.13**, mantendo a arquitetura padrão baseada em `x86_64`.

> <img width="831" height="388" alt="Captura de tela 2026-05-22 135127" src="https://github.com/user-attachments/assets/5a4778d1-3f83-4a23-aff1-094dc9654414" />

> Instanciação inicial da função Lambda com o ambiente de runtime Python 3.13 definido.

### Etapa 2: Implementação da Lógica do Jogo em Python
Substituiu-se o esqueleto de código padrão da AWS pelo script real da aplicação. A lógica foi estruturada da seguinte forma:
* Importação da biblioteca nativa `random` para computar um número randômico oculto de 1 a 10.
* Extração do parâmetro `palpite` enviado pela query string do evento de requisição HTTP (`queryStringParameters`).
* Validação condicional encadeada (`if`, `elif`, `else`) para checar se o número inserido é maior, menor ou idêntico ao número secreto.
* Construção do dicionário de resposta com o cabeçalho JSON (`Content-Type`) e o `statusCode: 200` exigidos pelo protocolo HTTP.

> <img width="836" height="387" alt="Captura de tela 2026-05-22 135356" src="https://github.com/user-attachments/assets/721edbeb-006f-4992-b892-46821a26b200" />

> Editor de código integrado do Lambda exibindo o script Python implementado e pronto para o deploy.

### Etapa 3: Criação e Configuração do Amazon API Gateway
Para que o navegador do usuário conseguisse se comunicar com o nosso código Python, expusemos um endpoint público utilizando uma **API HTTP**:
1. No console do **Amazon API Gateway**, criamos uma nova API chamada `API-FernandaOliveira`.
2. Mapeamos uma rota específica utilizando o método HTTP **`GET`** no recurso `/jogo`.
3. Definimos a integração dessa rota apontando diretamente para o destino da nossa função Lambda (`LambdaGame-FernandaOliveira`).

> <img width="1263" height="588" alt="image" src="https://github.com/user-attachments/assets/ac2ff859-1d61-4b3d-bfdc-e37fceb4cbfa" />


> Criação da API HTTP no console do API Gateway especificando a integração nativa com o AWS Lambda.

> <img width="1268" height="618" alt="image" src="https://github.com/user-attachments/assets/999095b6-6c20-49e1-8d3c-ce78ea7e567b" />


> Definição da rota GET /jogo vinculada ao microsserviço de backend.

4. Configuramos as etapas de implantação (*Stages*). O ambiente foi publicado no estágio padrão **`prod`** com a propriedade de *Auto-deploy* (implantação automática de alterações) ativada.

> <img width="1281" height="593" alt="image" src="https://github.com/user-attachments/assets/0889a166-48b1-49e3-9803-6da1d79fe444" />

> Configuração do estágio de implantação 'prod' da API.

> <img width="1249" height="590" alt="image" src="https://github.com/user-attachments/assets/901c13bf-0635-4442-ba60-bc5e75963eac" />

> Visualização do estágio publicado e captura da URL de Invocação gerada pela AWS.

### Etapa 4: Mitigação de Restrições de CORS
Como o nosso frontend e o nosso backend rodam em domínios diferentes da AWS, os navegadores bloqueariam as requisições por padrão. Para resolver isso, configurou-se as políticas de **CORS (Cross-Origin Resource Sharing)** diretamente na API:
* **Access-Control-Allow-Origin:** `*` (Permitindo requisições vindas de qualquer origem, essencial para ambientes de teste).
* **Access-Control-Allow-Headers:** `content-type`
* **Access-Control-Allow-Methods:** `GET`

> <img width="1262" height="585" alt="image" src="https://github.com/user-attachments/assets/68eb80ce-564e-4e45-b59c-55df6436ba73" />

> Painel de gerenciamento de CORS no API Gateway garantindo a liberação de requisições de origens cruzadas.

### Etapa 5: Provisionamento e Configuração do Amazon S3
Com a infraestrutura de backend operacional, configuramos o armazenamento e a distribuição estável do frontend.
1. Criamos um bucket S3 com o nome exclusivo `s3-website-fernandaoliveira`.

> <img width="1247" height="590" alt="image" src="https://github.com/user-attachments/assets/7ad0b84b-7dfe-4413-8b38-d7c817f50b40" />

> Menu de criação de buckets do Amazon S3 na região us-east-1.

2. Atualizamos o script JavaScript local contido no arquivo `index.html`, inserindo a URL de Invocação do API Gateway obtida no passo 3 dentro da função `fetch()`. Em seguida, efetuamos o upload do arquivo para o bucket.

> <img width="1250" height="586" alt="image" src="https://github.com/user-attachments/assets/ebb60d7a-9e6d-42ab-b4ab-1dcad59ed5cd" />

> Console de upload do Amazon S3 confirmando o envio do arquivo index.html.

3. Navegamos até a aba de propriedades do bucket e ativamos o recurso **Hospedagem de site estático (Static website hosting)**, definindo o arquivo `index.html` como o documento de índice principal.

> <img width="1260" height="587" alt="image" src="https://github.com/user-attachments/assets/aeb441d6-88cb-4001-9348-5c98d7fecfac" />


> Ativação e configuração das propriedades de hospedagem web no S3.

> <img width="1261" height="590" alt="image" src="https://github.com/user-attachments/assets/ca0ff854-6328-4f68-97be-63de461f4f9b" />

> URL pública gerada pelo S3 para acesso direto à aplicação web.

4. Por padrão, a AWS bloqueia acessos públicos ao S3. Para expor o jogo na internet, desativamos a caixa global de **"Bloquear todo o acesso público" (Block all public access)**.

> <img width="1251" height="611" alt="image" src="https://github.com/user-attachments/assets/38ffaf22-62fb-4029-9f99-3d77f2ea9678" />
> <img width="1360" height="355" alt="image" src="https://github.com/user-attachments/assets/7bd1c14b-f61d-4f20-a258-979578324032" />


> Alteração das configurações de visibilidade do bucket para permissão pública de leitura.

5. Para finalizar a segurança da camada de apresentação, aplicamos uma **S3 Bucket Policy** em formato JSON, concedendo explicitamente o efeito `Allow` para a ação `s3:GetObject` em todos os recursos internos daquele bucket.

> <img width="1266" height="619" alt="image" src="https://github.com/user-attachments/assets/192d921b-b220-43f0-ae27-b26de408746d" />


> Aplicação e salvamento da Bucket Policy estruturada em formato JSON.

---

## 🎯 Testes e Evidências do Resultado Final

Acessando a URL pública gerada pelo endpoint do Amazon S3, validamos com sucesso a integração de ponta a ponta em tempo real.

<img width="802" height="414" alt="image" src="https://github.com/user-attachments/assets/760c22db-4cf4-4c7c-9182-c6eb62ca0d91" />

* **Cenário de Teste 1:** Quando o usuário submete um palpite menor do que o número randômico armazenado em memória na execução do Lambda, o backend computa o valor e envia a dica instantaneamente de volta para a tela:


* **Cenário de Teste 2:** Ao persistir e inserir o número exato, a função Lambda valida a igualdade e retorna a flag de sucesso, atualizando a interface gráfica com a tela azul de vitória:


---

## 🧼 Ciclo de Vida e Desestruturação de Recursos (FinOps)

Seguindo as melhores práticas recomendadas pela **Escola da Nuvem**, uma vez concluída e validada a homologação do software, iniciou-se o processo completo de *Clean-up* (limpeza). Essa rotina é vital em ambientes corporativos de engenharia de nuvem para evitar desperdício financeiro e recursos órfãos (*zombie resources*).

### 1. Limpeza de Dados e Exclusão do Amazon S3
A console da AWS impede por segurança a deleção de buckets que contenham dados. O processo seguiu o fluxo correto de purga:
* Selecionou-se o bucket e executou-se a ação de esvaziamento, sendo exigido digitar a frase de segurança `"excluir permanentemente"`.

Com o bucket completamente limpo, realizou-se a sua exclusão total inserindo o nome exato do recurso (`s3-website-fernandaoliveira`) na caixa de texto.

### 2. Deleção da Camada de APIs (Amazon API Gateway)
Acessou-se o menu de gerenciamento de APIs, selecionando o recurso criado no laboratório e disparando a remoção imediata do endpoint do ecossistema.


### 3. Desprovimento do Computo (AWS Lambda)
Por fim, acessamos a lista de funções do console AWS Lambda para eliminar o backend serverless.
* Localizou-se o recurso `LambdaGame-FernandaOliveira` na tabela ativa.

* Através do botão superior "Ações", confirmou-se a remoção final do microsserviço da conta AWS.

---
🔬 *Projeto técnico prático elaborado com a infraestrutura fornecida e orientada pela Escola da Nuvem.*
