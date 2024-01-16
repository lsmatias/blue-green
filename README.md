# Blue-Green Deployment com Azure DevOps e Azure AppServices

O Blue-Green Deployment é uma estratégia de gerenciamento de lançamento de software (Deploy/Release) que visa minimizar o tempo de inatividade e reduzir o risco associado à implementação de novas versões de um aplicativo. A ideia básica é ter dois ambientes idênticos, rotulados como "Blue" e "Green", sendo que apenas um deles atende ao tráfego de produção ao vivo a qualquer momento.

<img width="355" alt="image" src="https://github.com/lsmatias/blue-green/assets/28391885/6e4112d9-c446-410d-8803-ab3a488ba71f">

 **Blue Environment (Production):** O ambiente de produção atual onde seu aplicativo está em execução.
  
<img width="239" alt="image" src="https://github.com/lsmatias/blue-green/assets/28391885/99984276-5598-4380-8266-bf5c4a2d7676">

 **Green Environment (Staging):** Um ambiente duplicado que espelha o ambiente de produção. Ele é preparado e testado com a nova versão do aplicativo.

<img width="239" alt="image" src="https://github.com/lsmatias/blue-green/assets/28391885/541b248a-c90d-4d84-a4ff-ac8bf13a12ab">

Antes de iniciar o processo de implantação, uma cópia exata do ambiente de produção **"Blue Environment Production"** é criada para formar o **"Green Environment (Staging)"**. Isso inclui replicar configurações de servidor, banco de dados e quaisquer outros componentes necessários.

A nova versão do aplicativo é implantada no **Green Environment (Staging)**. Isso pode envolver a atualização de código, bibliotecas, configurações e outros elementos necessários para a versão mais recente.

                                                                                         (Merged)
    (main) v1.0-----------------------------------------------------------------------------o----> Release v1.1
      |                                                                                    /
      ↓                                                                                   /
      o ------> [Feature/nova-funcionalidade]                                            / 
             \                                                                          /
              \                                                                        /
               o --- Commits ---- Open a Pull  --- Discuss and Review Commits --- Deploy/DevTest/Hml

Uma vez que a nova versão está implantada, são realizados diversos testes no "**Green Environment (Staging)**". Isso pode incluir:
* **Testes Funcionais:** Verificação de que todas as funcionalidades estão operando conforme o esperado.
* **Testes de Desempenho:** Avaliação do desempenho da aplicação sob carga para garantir que ela atenda aos requisitos de desempenho.
* **Testes de Integração:** Verificação da interação harmoniosa entre diferentes componentes do sistema.
* **Testes de Usabilidade:** Avaliação da experiência do usuário para garantir que não haja problemas de usabilidade.
* **Aprovação:** Se os testes forem bem-sucedidos e a nova versão for aprovada, o "**Green Environment (Staging)**" é considerado pronto para a produção.
* **Switch (Flip ou SWAP):** Após a aprovação, ocorre o interruptor, redirecionando o tráfego de produção do "**Blue Environment Production**" para o "**Green Environment (Staging)**". Os usuários agora acessam a nova versão do aplicativo.

## Monitoramento:
Uma vez que os testes são bem-sucedidos, é feito um Switch ou atualização DNS para redirecionar o tráfego de produção do **Blue Environment** para Green **Environment**". Isso torna a nova versão do aplicativo ao vivo. O sistema é monitorado de perto após o switch para garantir que a nova versão no ambiente de produção (anteriormente "**Green Environment**") esteja se comportando conforme o esperado em condições reais.


<img width="390" alt="image" src="https://github.com/lsmatias/blue-green/assets/28391885/c2621159-da2f-49ae-a2a5-efcd4a723579">

## Rollback:

Se ocorrerem problemas após o switch, é fácil reverter redirecionando o tráfego de volta para o ambiente original, realizado o SWAP.

<img width="562" alt="image" src="https://github.com/lsmatias/blue-green/assets/28391885/d3cc6bbc-a038-4336-a403-e0b757fcb67a">

Em resumo, se tudo estiver funcionando conforme o esperado no "**Green Environment**" após a implantação, não há necessidade de reverter, e o sistema continua operando na nova versão. A reversão é uma opção disponível apenas para cenários em que surgem problemas inesperados e é necessário voltar à versão anterior para evitar impactos negativos na experiência do usuário ou na operação do sistema.

<img width="398" alt="image" src="https://github.com/lsmatias/blue-green/assets/28391885/0dcf8461-7b88-4a68-8c15-e450cbaa072d">


## Vantagens do Blue-Green Deployment:

* Tempo de Inatividade Mínimo: Como a troca entre os ambientes é rápida, o tempo de inatividade é minimizado.

* Reversão Fácil (Rollback): Se ocorrerem problemas após a implementação, reverter é tão simples quanto redirecionar o tráfego de volta para o ambiente original.

* Mitigação de Riscos: Ao testar completamente em um ambiente separado, o risco de implantar uma versão defeituosa na produção é reduzido.

>É importante observar que, embora o **Blue-Green Deployment** seja uma estratégia poderosa, pode envolver custos adicionais de infraestrutura e recursos devido à manutenção de ambientes paralelos. Ferramentas e scripts automatizados de implementação podem facilitar significativamente o processo.

# Configurando no Azure DevOps

Agora que você já sabe o que é **Blue-Green Deployment**, vamos para a parte mais legal deste trabalho que é configurar essa arquitetura no Azure Pipelines.

Vamos usar usar o Azure App Service para criar um **Deployment Slot** secundário para seus servidores de produção e, em seguida, alternar entre os dois. Isso permitirá que você faça implantações a qualquer hora do dia, pois você terá dois ambientes de produção em execução.

Em um ambiente padrão do Azure DevOps , você geralmente tem quatro ambientes: **Dev**, **Test**, **Stage** e **Prod**. Para criar uma estratégia de implantação **Blue/Grenn**, você precisará adicionar dois estágios extras, o **Staging-Blue** e **Prod-Blue** conforme imagem abaixo:

<img width="589" alt="image" src="https://github.com/lsmatias/blue-green/assets/28391885/4c9b6837-5bc9-48f5-aa16-2005162af151">

## Configurando suas implantações Blue/Green
1 Acesse o Portal do Azure e selecione AppServices

2 A partir daí, você precisará abrir o aplicativo para o qual deseja configurar implantações **Blue/Green** e selecionar **Deployment Slot**.

<img width="594" alt="image" src="https://github.com/lsmatias/blue-green/assets/28391885/9c21892f-cee0-493a-ad57-e4659befc1f0">


Depois de abrir o **Deployment Slot** , você poderá adicionar um novo **slot** para seu ambiente **Blue**. Depois de preenchê-lo com as informações necessárias e criá-lo, você notará que não há tráfego nele. Isso é exatamente o que você deseja ver, porque este é o novo site de produção para o qual sua equipe enviará as alterações. 

<img width="725" alt="image" src="https://github.com/lsmatias/blue-green/assets/28391885/237c2dd2-b59f-4a81-9a66-9d5e2d34b4ad">


## Implantação do Prod-Blue e depois o Swithc:

Depois de atualizar todas as configurações, você deseja implantar a versão mais recente do aplicativo no **Prod-Blue**. Em seguida, acontecerá a validação manualmente as alterações de controle de qualidade deverá executar seu conjunto de testes para esta versão específica. 

Para o Switch, basta clicar no botão **Swap**  . Seu novo lançamento já está disponível com todas as alterações. É tão fácil! Leva apenas alguns segundos e você pode fazer isso a qualquer hora do dia sem interromper o site. Agora, seu site ativo está no **Prod-Blue** e você enviará sua nova versão para o **Prod Green**.



<img width="547" alt="image" src="https://github.com/lsmatias/blue-green/assets/28391885/e6fbd49e-c2b4-4970-997d-b13af1bcb1b0">

## Concluir sua implantação Blue/Green

<img width="270" alt="image" src="https://github.com/lsmatias/blue-green/assets/28391885/7aee8618-dd23-4cb0-9a63-fd4404671c46">

Agora você concluiu suas implantações **Blue/Green** com o Azure. Na próxima vez que você fizer uma implantação, você a implantará para validar no **Green**. Nas etapas acima, você trocou os slots de produção para direcionar o tráfego do **Green** para o **Blue**. Agora você implantará no verde, fará testes e validação e, em seguida, trocará de site, para que o tráfego seja direcionado do **Blue** para o **Green**. 


<img width="377" alt="image" src="https://github.com/lsmatias/blue-green/assets/28391885/f4966898-2efc-42bb-8ac3-04fb8c5ca25d">

Seu site de produção disponivel sempre mudará de cor entre os Releases.

>Blue-Green Deployment não é específico para o ambiente Azure; é uma prática geral de implantação que pode ser aplicada em diversas plataformas e ambientes de hospedagem, incluindo o Azure, AWS (Amazon Web Services), Google Cloud Platform e ambientes on-premise.

Independentemente da plataforma em que você está hospedando seus aplicativos (Azure, AWS, GCP, data centers locais, etc.), você pode implementar o modelo Blue-Green Deployment usando as ferramentas e recursos disponíveis na plataforma específica ou utilizando soluções de automação e gerenciamento de configuração independentes da plataforma.


