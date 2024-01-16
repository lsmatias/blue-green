# Blue-green deployment com Azure DevOps e Azure AppServices

<img width="355" alt="image" src="https://github.com/lsmatias/blue-green/assets/28391885/6e4112d9-c446-410d-8803-ab3a488ba71f">


O Blue-Green Deployment é uma estratégia de gerenciamento de lançamento de software (Deploy/Release) que visa minimizar o tempo de inatividade e reduzir o risco associado à implementação de novas versões de um aplicativo. A ideia básica é ter dois ambientes idênticos, rotulados como "Blue" e "Green", sendo que apenas um deles atende ao tráfego de produção ao vivo a qualquer momento.

* **Blue Environment (Production):** O ambiente de produção atual onde seu aplicativo está em execução.
  
<img width="239" alt="image" src="https://github.com/lsmatias/blue-green/assets/28391885/99984276-5598-4380-8266-bf5c4a2d7676">

 * **Green Environment (Staging):** Um ambiente duplicado que espelha o ambiente de produção. Ele é preparado e testado com a nova versão do aplicativo.

<img width="239" alt="image" src="https://github.com/lsmatias/blue-green/assets/28391885/541b248a-c90d-4d84-a4ff-ac8bf13a12ab">

Antes de iniciar o processo de implantação, uma cópia exata do ambiente de produção **"Blue Environment Production"** é criada para formar o **"Green Environment (Staging)"**. Isso inclui replicar configurações de servidor, banco de dados e quaisquer outros componentes necessários.

A nova versão do aplicativo é implantada no **Green Environment (Staging)**. Isso pode envolver a atualização de código, bibliotecas, configurações e outros elementos necessários para a versão mais recente.

                                                                                         (Merged)
    (main) v1.0-----------------------------------------------------------------------------o----> Release v1.1
      |                                                                                    /
      ↓                                                                                   /
      o ----   Feature/nova-funcionalidade                                               / 
             \                                                                          /
              \                                                                        /
               o --- Commits ---- Open a Pull  --- Discuss and Review Commits --- Deploy/DevTest/Hml

