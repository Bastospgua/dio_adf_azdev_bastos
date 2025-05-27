# dio_adf_azdev_bastos

# Integração do Azure Data Factory com Azure DevOps para Versionamento e Backups

Este projeto demonstra como integrar o Azure Data Factory (ADF) ao Azure DevOps, permitindo versionamento, controle de mudanças e backups automáticos de pipelines e artefatos de dados. A integração garante maior 
governança e rastreabilidade no desenvolvimento de soluções de dados.

**Nomes Utilizados (Exemplo):**
*   **Azure Data Factory:** `adf-dio-bastos`
*   **Nó de Integration Runtime (Exemplo):** `BS_BASTOS` (Referenciado nas configurações do ADF)
*   **Banco de Dados (Exemplo):** `ItibereBD` (Utilizado como fonte/destino nos pipelines)

## Visão Geral do Processo

O processo envolve a configuração do Azure DevOps para hospedar o código-fonte do Data Factory (em formato JSON) e a conexão do ADF a este repositório Git. Isso permite que todas as alterações feitas na interface do ADF 
sejam rastreadas, versionadas e passíveis de revisão e colaboração, além de servir como um backup natural dos seus artefatos.

## Passo a Passo da Integração

A seguir, detalhamos as etapas para configurar a integração.

### 1. Criação da Organização e Projeto no Azure DevOps

Antes de conectar o ADF, é necessário ter um local no Azure DevOps para armazenar o código.

*   **Ação:** Acesse o portal do Azure DevOps (`dev.azure.com`).
*   **Ação:** Crie uma nova Organização, caso ainda não possua uma (nome da organização: BS_Caicara).
*   **Ação:** Dentro da Organização, crie um novo Projeto (proj_integra_itibere).
    *   Selecione o controle de versão como **Git**.

```
[PRINT SIMULADO: Tela do Azure DevOps]
Descrição: Imagem mostrando o formulário de criação de novo projeto no Azure DevOps.
Campos visíveis: Nome do Projeto (\"proj_integra_itibere\")Descrição (opcional),
Visibilidade (Privado/Público), Controle de Versão selecionado como "Git".
Botão "Criar" destacado.
```

### 2. Configuração do Repositório Git no Azure DevOps

Dentro do projeto recém-criado, um repositório Git inicial (geralmente com o mesmo nome do projeto) é criado automaticamente. Vamos configurar a branch principal.

*   **Ação:** Navegue até a seção "Repos" dentro do seu projeto no Azure DevOps.
*   **Ação:** Certifique-se de que o repositório (`proj_integra_itibere`) está selecionado.
*   **Ação:** Por padrão, a branch principal pode ser `main` ou `master`. O ADF geralmente trabalha com uma branch de colaboração (ex: `main` ou `develop`) e publica em uma branch específica (`adf_publish`). É uma boa 
prática inicial ter a `main` como branch principal.

```
[PRINT SIMULADO: Tela de Repos no Azure DevOps]
Descrição: Imagem mostrando a interface de "Repos" no Azure DevOps.
Painel esquerdo: Nome do projeto e repositório selecionado(\"proj_integra_itibere\")
Área principal: Mensagem indicando um repositório vazio ou com um README inicial.
Menu superior: Opções como "Files", "Commits", "Pushes", "Branches".
Dropdown de seleção de branch visível, mostrando a branch atual (ex: "main").
```

### 3. Conectando o Azure Data Factory ao Repositório Git

Esta é a etapa central onde conectamos a instância do ADF ao repositório DevOps.

*   **Ação:** Abra o portal do Azure e navegue até sua instância do Azure Data Factory (`adf-dio-bastos`).
*   **Ação:** Na página de visão geral do ADF, clique em "Abrir o Azure Data Factory Studio (Criar e Monitorar)".
*   **Ação:** Dentro do ADF Studio, vá para a aba "Gerenciar" (ícone de caixa de ferramentas).
*   **Ação:** Na seção "Controle do código-fonte", clique em "Configurar repositório Git".
*   **Ação:** Selecione o tipo de repositório: **Azure DevOps Git**.
*   **Ação:** Selecione sua organização Azure DevOps e o projeto (`proj_integra_itibere`).
*   **Ação:** Selecione o repositório (`proj_integra_itibere`).
*   **Ação:** Escolha a **Branch de colaboração**: `main` (ou a branch que você definiu como principal para desenvolvimento).
*   **Ação:** Defina o **Diretório raiz**: `/` (ou um subdiretório, se preferir organizar melhor, ex: `/adf-dio-bastos/`). Todos os artefatos do ADF (pipelines, datasets, etc.) serão salvos dentro desta pasta no 
repositório.
*   **Ação:** Marque a opção **Importar recursos existentes do Data Factory para o repositório**. Isso levará todos os pipelines e outros artefatos que já existam no seu ADF (se houver) para o Git como um commit inicial.
*   **Ação:** Escolha a **Branch de publicação**: `adf_publish` (este é o padrão e recomendado). Esta branch conterá os modelos ARM gerados automaticamente pelo ADF quando você publica suas alterações. Ela é usada para 
implantações em outros ambientes (CI/CD).
*   **Ação:** Clique em "Aplicar".

```
[PRINT SIMULADO: Tela de Configuração do Repositório Git no ADF Studio]
Descrição: Imagem mostrando a janela de configuração do repositório Git dentro do ADF Studio.
Opções visíveis: Tipo de Repositório ("Azure DevOps Git" selecionado)Organização Azure DevOps (dropdown com \"BS_Caicara\" selecionada)Projeto (\"proj_integra_itibere\" selecionadoRepositório (\"proj_integra_itibere\" 
selecionado)Branch de Colaboração ("main" selecionada),
Diretório Raiz ("/" preenchido),
Checkbox "Importar recursos existentes" marcado,
Branch de Publicação ("adf_publish" preenchida).
Botão "Aplicar" na parte inferior.
```

Após aplicar, o ADF Studio recarregará e você verá um dropdown no topo indicando a branch Git atual (`main`). Agora, qualquer alteração (criar/editar pipeline, dataset, etc.) precisará ser salva ("Salvar tudo") e depois 
"commitada" para o repositório Git.

### 4. Trabalhando com Versionamento: Salvando e Commitando Alterações

Com a integração ativa, o fluxo de trabalho muda ligeiramente:

*   **Ação:** Crie ou modifique um pipeline, dataset, fluxo de dados, etc., no ADF Studio (ex: um pipeline que usa o Integration Runtime `BS_BASTOS` para conectar ao banco `ItibereBD`).
*   **Ação:** Após fazer as alterações desejadas, clique no botão "Salvar tudo". Isso salva as alterações localmente na sua sessão do ADF Studio.
*   **Ação:** O indicador de branch no topo mostrará que há alterações pendentes (geralmente com um asterisco ou número).
*   **Ação:** Clique no dropdown da branch e selecione "Criar commit" (ou similar).
*   **Ação:** Uma janela aparecerá mostrando os arquivos JSON modificados, adicionados ou excluídos. Escreva uma mensagem de commit descritiva (ex: "Adiciona pipeline de cópia de dados de ItibereBD").
*   **Ação:** Clique em "Commit". Isso envia as alterações para a sua branch de colaboração (`main`) no Azure DevOps.

```
[PRINT SIMULADO: ADF Studio com Alterações Pendentes e Commit]
Descrição: Imagem mostrando a interface do ADF Studio.
Topo da tela: Dropdown de branch mostrando "main*" (indicando alterações não commitadas).
Ao lado: Botão "Salvar tudo".
Após clicar no dropdown: Opção "Criar commit".
Janela de Commit: Lista de arquivos JSON (ex: /pipeline/pipeline_copia_ItibereBD.json, /dataset/dataset_ItibereBD.json) com status (modificado/adicionado).
Campo para mensagem de commit preenchido.
Botão "Commit".
```

### 5. Visualizando Arquivos Versionados no Azure DevOps

Você pode ver como o ADF organiza seus artefatos no repositório Git.

*   **Ação:** Volte para o seu projeto (`proj_integra_itibere`) no Azure DevOps.
*   **Ação:** Navegue até "Repos" -> "Files".
*   **Ação:** Certifique-se de que a branch `main` está selecionada.
*   **Ação:** Você verá pastas como `pipeline`, `dataset`, `linkedservice`, `integrationRuntime`, etc., dentro do diretório raiz que você configurou.
    *   Dentro de `integrationRuntime`, por exemplo, você poderia encontrar um arquivo JSON descrevendo a configuração do seu nó `BS_BASTOS` (se ele for um Self-Hosted IR gerenciado pelo ADF).
    *   Dentro de `linkedservice`, você encontraria a definição da conexão com `ItibereBD`.
    *   Dentro de `pipeline`, o JSON do pipeline que utiliza esses recursos.
*   **Ação:** Você pode clicar nos arquivos JSON para ver o código-fonte exato dos seus artefatos do ADF.
*   **Ação:** Navegue até "Commits" para ver o histórico de alterações, quem fez, quando e a mensagem de commit.

```
[PRINT SIMULADO: Estrutura de Pastas no Repositório Azure DevOps]
Descrição: Imagem da seção "Files" do repositório no Azure DevOps.
Estrutura de pastas visível: /pipeline, /dataset, /linkedservice, /integrationRuntime, /trigger, etc.
Dentro da pasta /pipeline: Arquivo "pipeline_copia_ItibereBD.json".
Dentro da pasta /linkedservice: Arquivo "LS_SQLServer_ItibereBD.json".
Dentro da pasta /integrationRuntime: Arquivo "IR_SelfHosted_BS_BASTOS.json" (exemplo).
```

### 6. Publicando Alterações (Gerando Modelo ARM)

Quando um conjunto de alterações na branch de colaboração (`main`) está pronto para ser implantado (ex: em um ambiente de Teste ou Produção), você usa o processo de "Publicar" no ADF.

*   **Ação:** No ADF Studio, certifique-se de que todas as alterações desejadas estão commitadas na branch `main`.
*   **Ação:** Clique no botão "Publicar".
*   **Ação:** O ADF compilará os artefatos da branch `main` e gerará modelos ARM (Azure Resource Manager) na branch `adf_publish`.
*   **Ação:** Esses modelos ARM contêm a definição completa do seu Data Factory e podem ser usados em pipelines de CI/CD do Azure DevOps para automatizar a implantação.

```
[PRINT SIMULADO: Botão Publicar no ADF Studio e Branch adf_publish no DevOps]
Descrição: Duas imagens lado a lado ou sequenciais.
1. ADF Studio: Botão "Publicar" destacado na interface.
2. Azure DevOps Repos: Branch "adf_publish" selecionada, mostrando arquivos como "ARMTemplateForFactory.json" e "ARMTemplateParametersForFactory.json".
```

## Boas Práticas

*   **Configurar Git na Criação:** É ideal configurar a integração Git ao criar o Data Factory, mas é possível fazer depois (como demonstrado).
*   **Branches:** Use branches para funcionalidades (`feature/minha-feature`), correções (`hotfix/corrige-bug`) e faça Pull Requests para mesclar na branch de colaboração (`main` ou `develop`). Isso melhora a revisão de 
código e colaboração.
*   **Commits:** Faça commits pequenos e focados com mensagens claras.
*   **Branch `adf_publish`:** Nunca trabalhe diretamente nesta branch. Ela é gerenciada automaticamente pelo ADF.
*   **Segredos:** Não armazene senhas ou chaves diretamente nos Linked Services no Git. Use o Azure Key Vault para gerenciar segredos de forma segura.




## Insights e Possibilidades Aprendidas

A integração do Azure Data Factory com o Azure DevOps transcende o simples versionamento de código; ela abre um leque de possibilidades para aprimorar a governança, a colaboração e a automação no ciclo de vida do 
desenvolvimento de dados. Ao adotar essa prática, percebemos ganhos significativos em diversas frentes.

Primeiramente, a **rastreabilidade** se torna inerente ao processo. Cada alteração, desde a criação de um simples dataset até a modificação complexa de um pipeline que interage com recursos como o Integration Runtime 
`BS_BASTOS` ou o banco de dados `ItibereBD`, fica registrada no histórico de commits do Git. Isso não apenas facilita a auditoria, mas também permite entender a evolução da solução de dados ao longo do tempo, identificar 
quem realizou determinada mudança e quando, o que é crucial em ambientes regulados ou com equipes maiores.

O **controle de mudanças** é outro benefício direto. Ao invés de alterações serem aplicadas diretamente no ambiente de produção ou mesmo de desenvolvimento sem um processo formal, a integração com DevOps incentiva (e pode 
obrigar, através de políticas de branch) o uso de Pull Requests. Isso significa que as alterações propostas em branches de feature podem ser revisadas por pares antes de serem incorporadas à branch principal (`main`). 
Revisores podem analisar o código JSON gerado, verificar a lógica do pipeline, garantir a conformidade com padrões e identificar potenciais problemas antes que eles impactem outros processos. Esse fluxo de revisão por 
pares melhora drasticamente a qualidade e a robustez das soluções de dados.

Em termos de **colaboração**, o repositório Git centralizado no Azure DevOps se torna a única fonte da verdade para o código do Data Factory. Múltiplos desenvolvedores podem trabalhar simultaneamente em diferentes 
funcionalidades, cada um em sua própria branch, sem o risco de sobrescrever o trabalho alheio. A mesclagem (merge) das alterações é gerenciada pelo Git, facilitando a integração do trabalho de toda a equipe de forma 
organizada.

Olhando para o futuro, a **automação via CI/CD (Integração Contínua e Entrega Contínua)** é talvez a possibilidade mais impactante. A branch `adf_publish`, que contém os modelos ARM gerados automaticamente, é a base para 
a criação de pipelines de release no Azure DevOps. É possível configurar gatilhos para que, toda vez que uma nova versão for publicada na `adf_publish` (após um merge bem-sucedido na `main` e o clique no botão "Publicar" 
no ADF), um pipeline de release seja acionado automaticamente. Esse pipeline pode pegar o modelo ARM e implantá-lo em ambientes subsequentes, como Teste (QA), Homologação (UAT) ou Produção (PROD). Isso não só acelera o 
processo de implantação, mas também o torna mais confiável e repetível, eliminando erros manuais. A parametrização dos modelos ARM permite que configurações específicas de cada ambiente (como connection strings de bancos 
de dados ou nomes de recursos) sejam ajustadas durante a implantação, garantindo que o mesmo código funcione corretamente em diferentes estágios.

Além disso, a integração serve como um mecanismo eficaz de **backup e recuperação de desastres**. Todo o código-fonte do seu Data Factory está seguro no repositório Git. Caso ocorra algum problema grave na instância do 
ADF, ou mesmo se ela for acidentalmente excluída, é possível recriar a instância e restaurar todos os pipelines, datasets e demais artefatos diretamente a partir do repositório Git, minimizando o tempo de inatividade e a 
perda de trabalho.

Finalmente, essa abordagem facilita a **padronização entre ambientes**. Ao usar o mesmo código-fonte (gerenciado no Git) e implantá-lo através de pipelines de CI/CD parametrizados, garantimos que os ambientes de 
Desenvolvimento, Teste e Produção sejam consistentes, reduzindo o clássico problema de "funciona na minha máquina" e facilitando a identificação e correção de bugs específicos de cada ambiente.

A adoção da integração ADF-DevOps, portanto, representa um salto de maturidade na gestão de projetos de dados, movendo de um desenvolvimento muitas vezes ad-hoc para um processo estruturado, controlado, colaborativo e 
automatizado.

