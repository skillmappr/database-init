Guia para criação da base de dados

# ETAPA REFERENCIAIS SOCIEDADE BRASILEIRA DA COMPUTAÇÃO #

## CRIAÇÃO DOS NÓS ##

### Criação de nós do tipo SBC_Eixo ###

LOAD CSV WITH HEADERS FROM 'https://raw.githubusercontent.com/skillmappr/database-init/main/Sociedade-Brasileira-da-Computacao/nodes/sbc-eixos.csv' as line
FIELDTERMINATOR ';'
CREATE (:SBC_Eixo{id:line.id, tipo: line.tipo, nome: line.nome, competenciaGeral: line.competenciaGeral})

### Criação de nós do tipo SBC_Conteudo ###

LOAD CSV WITH HEADERS FROM 'https://raw.githubusercontent.com/skillmappr/database-init/main/Sociedade-Brasileira-da-Computacao/nodes/sbc-conteudos.csv' AS line
FIELDTERMINATOR ';'
CREATE (:SBC_Conteudo{id:line.id, tipo: line.tipo, nome: line.nome})

### Criação de nós do tipo SBC_CompetenciaDerivada ###

LOAD CSV WITH HEADERS FROM 'https://raw.githubusercontent.com/skillmappr/database-init/main/Sociedade-Brasileira-da-Computacao/nodes/sbc-competencias-derivadas.csv' AS line
FIELDTERMINATOR ';'
CREATE (:SBC_CompetenciaDerivada{id:line.id, tipo: line.tipo, codigo: line.codigo, descricao:line.descricao})

### Criação de nós do tipo SBC_CompetenciaGeralEgresso ###

LOAD CSV WITH HEADERS FROM 'https://raw.githubusercontent.com/skillmappr/database-init/main/Sociedade-Brasileira-da-Computacao/nodes/sbc-competencias-gerais-egressos.csv' AS line
FIELDTERMINATOR ';'
CREATE (:SBC_CompetenciaGeralEgresso{id:line.id, tipo: line.tipo, descricao: line.descricao})

### Criação de nós do tipo SBC_CompetenciaEspecificaEgresso ###

LOAD CSV WITH HEADERS FROM 'https://raw.githubusercontent.com/skillmappr/database-init/main/Sociedade-Brasileira-da-Computacao/nodes/sbc-competencias-especificas-egressos.csv' AS line
FIELDTERMINATOR ';'
CREATE (:SBC_CompetenciaEspecificaEgresso{id:line.id, tipo: line.tipo, descricao: line.descricao})


## CRIAÇÃO DOS RELACIONAMENTOS ENTRE OS NÓS ##

### Criação do relacionamento entre SBC_Conteudo e SBC_CompetenciaDerivada ###

LOAD CSV WITH HEADERS FROM 'https://raw.githubusercontent.com/skillmappr/database-init/main/Sociedade-Brasileira-da-Computacao/relacionamentos/conteudo-para-competencia-derivada.csv' AS line
FIELDTERMINATOR ';'
MERGE (cont:SBC_Conteudo {id: line.idConteudo})
MERGE (comp:SBC_CompetenciaDerivada {id: line.idCompetenciaDerivada})

FOREACH(ignoreMe IN CASE WHEN line.Classificacao = "APLICAR" THEN [1] ELSE [] END |
 MERGE (cont)-[:APLICAR]->(comp))
FOREACH(ignoreMe IN CASE WHEN line.Classificacao = "AVALIAR" THEN [1] ELSE [] END |
 MERGE (cont)-[:AVALIAR]->(comp))
FOREACH(ignoreMe IN CASE WHEN line.Classificacao = "CRIAR" THEN [1] ELSE [] END |
 MERGE (cont)-[:CRIAR]->(comp))
FOREACH(ignoreMe IN CASE WHEN line.Classificacao = "ENTENDER" THEN [1] ELSE [] END |
 MERGE (cont)-[:ENTENDER]->(comp))
FOREACH(ignoreMe IN CASE WHEN line.Classificacao = "ANALISAR" THEN [1] ELSE [] END |
 MERGE (cont)-[:ANALISAR]->(comp))

### Criação do relacionamento entre SBC_Conteudo e SBC_Eixo ###

LOAD CSV WITH HEADERS FROM 'https://raw.githubusercontent.com/skillmappr/database-init/main/Sociedade-Brasileira-da-Computacao/relacionamentos/conteudo-para-eixo.csv' AS line
FIELDTERMINATOR ';'
MERGE (cont:SBC_Conteudo {id: line.idConteudo})
MERGE (eixo:SBC_Eixo {id: line.idEixo})
MERGE (cont)-[:PERTENCE]->(eixo)

LOAD CSV WITH HEADERS FROM 'https://raw.githubusercontent.com/skillmappr/database-init/main/Sociedade-Brasileira-da-Computacao/relacionamentos/conteudo-para-eixo.csv' AS line
FIELDTERMINATOR ';'
MERGE (cont:SBC_Conteudo {id: line.idConteudo})
MERGE (eixo:SBC_Eixo {id: line.idEixo})
MERGE (eixo)-[:POSSUI]->(cont)

### Criação do relacionamento entre SBC_Eixo e SBC_CompetenciaDerivada ###

LOAD CSV WITH HEADERS FROM 'https://raw.githubusercontent.com/skillmappr/database-init/main/Sociedade-Brasileira-da-Computacao/relacionamentos/eixo-para-competencia-derivada.csv' AS line
FIELDTERMINATOR ';'
MERGE (eixo:SBC_Eixo {id: line.idEixo})
MERGE (comp:SBC_CompetenciaDerivada {id: line.idCompetenciaDerivada})
MERGE (comp)-[:PERTENCE]->(eixo)

LOAD CSV WITH HEADERS FROM 'https://raw.githubusercontent.com/skillmappr/database-init/main/Sociedade-Brasileira-da-Computacao/relacionamentos/eixo-para-competencia-derivada.csv' AS line
FIELDTERMINATOR ';'
MERGE (eixo:SBC_Eixo {id: line.idEixo})
MERGE (comp:SBC_CompetenciaDerivada {id: line.idCompetenciaDerivada})
MERGE (eixo)-[:POSSUI]->(comp)

### Criação do relacionamento entre SBC_CompetenciaGeralEgresso e SBC_CompetenciaDerivada ###

LOAD CSV WITH HEADERS FROM 'https://raw.githubusercontent.com/skillmappr/database-init/main/Sociedade-Brasileira-da-Computacao/relacionamentos/competencia-geral-egressos-para-competencia-derivada.csv' AS line
FIELDTERMINATOR ';'
MERGE (compGeralEgresso:SBC_CompetenciaGeralEgresso {id: line.idCompetenciaGeralEgresso})
MERGE (compDerivada:SBC_CompetenciaDerivada {id: line.idCompetenciaDerivada})
MERGE (compDerivada)-[:CONTRIBUI_PARA]->(compGeralEgresso)

LOAD CSV WITH HEADERS FROM 'https://raw.githubusercontent.com/skillmappr/database-init/main/Sociedade-Brasileira-da-Computacao/relacionamentos/competencia-geral-egressos-para-competencia-derivada.csv' AS line
FIELDTERMINATOR ';'
MERGE (compGeralEgresso:SBC_CompetenciaGeralEgresso {id: line.idCompetenciaGeralEgresso})
MERGE (compDerivada:SBC_CompetenciaDerivada {id: line.idCompetenciaDerivada})
MERGE (compGeralEgresso)-[:OBTIDA_ATRAVES_DE]->(compDerivada)

### Criação do relacionamento entre SBC_CompetenciaEspecificaEgresso e SBC_CompetenciaDerivada ###

LOAD CSV WITH HEADERS FROM 'https://raw.githubusercontent.com/skillmappr/database-init/main/Sociedade-Brasileira-da-Computacao/relacionamentos/competencia-especifica-egressos-para-competencia-derivada.csv' AS line
FIELDTERMINATOR ';'
MERGE (compEspecificaEgresso:SBC_CompetenciaEspecificaEgresso {id: line.idCompetenciaEspecificaEgresso})
MERGE (compDerivada:SBC_CompetenciaDerivada {id: line.idCompetenciaDerivada})
MERGE (compDerivada)-[:CONTRIBUI_PARA]->(compEspecificaEgresso)

LOAD CSV WITH HEADERS FROM 'https://raw.githubusercontent.com/skillmappr/database-init/main/Sociedade-Brasileira-da-Computacao/relacionamentos/competencia-especifica-egressos-para-competencia-derivada.csv' AS line
FIELDTERMINATOR ';'
MERGE (compEspecificaEgresso:SBC_CompetenciaEspecificaEgresso {id: line.idCompetenciaEspecificaEgresso})
MERGE (compDerivada:SBC_CompetenciaDerivada {id: line.idCompetenciaDerivada})
MERGE (compEspecificaEgresso)-[:OBTIDA_ATRAVES_DE]->(compDerivada)


# ETAPA REFERENCIAIS BASE NACIONAL COMUM CURRICULAR #

## CRIAÇÃO DOS NÓS ##

### Criação de nós do tipo BNCC_AreaDeConhecimento ###

LOAD CSV WITH HEADERS FROM 'https://raw.githubusercontent.com/skillmappr/database-init/main/Base-Nacional-Comum-Curricular/nodes/bncc-areas-do-conhecimento.csv' AS line
FIELDTERMINATOR ';'
CREATE (:BNCC_AreaDeConhecimento{id:line.id, tipo: line.tipo, nome: line.nome})

### Criação de nós do tipo BNCC_CompetenciaGeral ###

LOAD CSV WITH HEADERS FROM 'https://raw.githubusercontent.com/skillmappr/database-init/main/Base-Nacional-Comum-Curricular/nodes/bncc-competencias-gerais.csv' AS line
FIELDTERMINATOR ';'
CREATE (:BNCC_CompetenciaGeral{id:line.id, tipo: line.tipo, descricao: line.descricao})


## Linguagem e suas Tecnologias ##

### Criação de nós do tipo BNCC_CompetenciaEspecifica ###

LOAD CSV WITH HEADERS FROM 'https://raw.githubusercontent.com/skillmappr/database-init/main/Base-Nacional-Comum-Curricular/nodes/Linguagem-e-suas-Tecnologias/bncc-competencias-especificas-ling-tecnologias.csv' AS line
FIELDTERMINATOR ';'
CREATE (:BNCC_CompetenciaEspecifica{id:line.id, tipo: line.tipo, descricao: line.descricao})

### Criação de nós do tipo BNCC_Habilidade ###

LOAD CSV WITH HEADERS FROM 'https://raw.githubusercontent.com/skillmappr/database-init/main/Base-Nacional-Comum-Curricular/nodes/Linguagem-e-suas-Tecnologias/bncc-habilidades-ling-tecnologias.csv' AS line
FIELDTERMINATOR ';'
CREATE (:BNCC_Habilidade{id:line.id, tipo: line.tipo, codigo: line.codigo, descricao: line.descricao})

### Criação de nós do tipo BNCC_Componente ###

LOAD CSV WITH HEADERS FROM 'https://raw.githubusercontent.com/skillmappr/database-init/main/Base-Nacional-Comum-Curricular/nodes/Linguagem-e-suas-Tecnologias/bncc-componentes-ling-tecnologias.csv' AS line
FIELDTERMINATOR ';'
CREATE (:BNCC_Componente{id:line.id, tipo: line.tipo, nome: line.nome})

### Criação de nós do tipo BNCC_HabilidadeDeComponente ###

LOAD CSV WITH HEADERS FROM 'https://raw.githubusercontent.com/skillmappr/database-init/main/Base-Nacional-Comum-Curricular/nodes/Linguagem-e-suas-Tecnologias/bncc-habilidades-compon-ling-portug.csv' AS line
FIELDTERMINATOR ';'
CREATE (:BNCC_HabilidadeDeComponente{id:line.id, tipo: line.tipo, codigo: line.codigo, descricao: line.descricao})


## Matemática e suas Tecnologias ##

### Criação de nós do tipo BNCC_CompetenciaEspecifica ###

LOAD CSV WITH HEADERS FROM 'https://raw.githubusercontent.com/skillmappr/database-init/main/Base-Nacional-Comum-Curricular/nodes/Matematica-e-suas-Tecnologias/bncc-competencias-especificas-mat-tecnologias.csv' AS line
FIELDTERMINATOR ';'
CREATE (:BNCC_CompetenciaEspecifica{id:line.id, tipo: line.tipo, descricao: line.descricao})

### Criação de nós do tipo BNCC_Habilidade ###

LOAD CSV WITH HEADERS FROM 'https://raw.githubusercontent.com/skillmappr/database-init/main/Base-Nacional-Comum-Curricular/nodes/Matematica-e-suas-Tecnologias/bncc-habilidades-mat-tecnologias.csv' AS line
FIELDTERMINATOR ';'
CREATE (:BNCC_Habilidade{id:line.id, tipo: line.tipo, codigo: line.codigo, descricao: line.descricao})

### Criação de nós do tipo BNCC_UnidadeDeEstudo ###

LOAD CSV WITH HEADERS FROM 'https://raw.githubusercontent.com/skillmappr/database-init/main/Base-Nacional-Comum-Curricular/nodes/Matematica-e-suas-Tecnologias/bncc-unid-estudo-mat-tecnologias.csv' AS line
FIELDTERMINATOR ';'
CREATE (:BNCC_UnidadeDeEstudo{id:line.id, tipo: line.tipo, nome: line.nome})


## Ciências da Natureza e suas Tecnologias ##

### Criação de nós do tipo BNCC_CompetenciaEspecifica ###

LOAD CSV WITH HEADERS FROM 'https://raw.githubusercontent.com/skillmappr/database-init/main/Base-Nacional-Comum-Curricular/nodes/Ciencias-da-Natureza-e-suas-Tecnologias/bncc-competencias-especificas-cien-nat-tecnologias.csv' AS line
FIELDTERMINATOR ';'
CREATE (:BNCC_CompetenciaEspecifica{id:line.id, tipo: line.tipo, descricao: line.descricao})

### Criação de nós do tipo BNCC_Habilidade ###

LOAD CSV WITH HEADERS FROM 'https://raw.githubusercontent.com/skillmappr/database-init/main/Base-Nacional-Comum-Curricular/nodes/Ciencias-da-Natureza-e-suas-Tecnologias/bncc-habilidades-cien-nat-tecnologias.csv' AS line
FIELDTERMINATOR ';'
CREATE (:BNCC_Habilidade{id:line.id, tipo: line.tipo, codigo: line.codigo, descricao: line.descricao})

### Criação de nós do tipo BNCC_Componente ###

LOAD CSV WITH HEADERS FROM 'https://raw.githubusercontent.com/skillmappr/database-init/main/Base-Nacional-Comum-Curricular/nodes/Ciencias-da-Natureza-e-suas-Tecnologias/bncc-componentes-cien-nat-tecnologias.csv' AS line
FIELDTERMINATOR ';'
CREATE (:BNCC_Componente{id:line.id, tipo: line.tipo, nome: line.nome})


## Ciências Humanas e Sociais Aplicadas ##

### Criação de nós do tipo BNCC_CompetenciaEspecifica ###

LOAD CSV WITH HEADERS FROM 'https://raw.githubusercontent.com/skillmappr/database-init/main/Base-Nacional-Comum-Curricular/nodes/Ciencias-Humanas-e-Sociais-Aplicadas/bncc-competencias-especificas-cien-hum-sociais.csv' AS line
FIELDTERMINATOR ';'
CREATE (:BNCC_CompetenciaEspecifica{id:line.id, tipo: line.tipo, descricao: line.descricao})

### Criação de nós do tipo BNCC_Habilidade ###

LOAD CSV WITH HEADERS FROM 'https://raw.githubusercontent.com/skillmappr/database-init/main/Base-Nacional-Comum-Curricular/nodes/Ciencias-Humanas-e-Sociais-Aplicadas/bncc-habilidades-cien-hum-sociais.csv' AS line
FIELDTERMINATOR ';'
CREATE (:BNCC_Habilidade{id:line.id, tipo: line.tipo, codigo: line.codigo, descricao: line.descricao})

### Criação de nós do tipo BNCC_Componente ###

LOAD CSV WITH HEADERS FROM 'https://raw.githubusercontent.com/skillmappr/database-init/main/Base-Nacional-Comum-Curricular/nodes/Ciencias-Humanas-e-Sociais-Aplicadas/bncc-componentes-cien-hum-sociais.csv' AS line
FIELDTERMINATOR ';'
CREATE (:BNCC_Componente{id:line.id, tipo: line.tipo, nome: line.nome})


## CRIAÇÃO DOS RELACIONAMENTOS ##


## Linguagem e suas Tecnologias ##

### Criação do relacionamento entre BNCC_Componente (Língua Portuguesa) e BNCC_HabilidadeDeComponente ###

LOAD CSV WITH HEADERS FROM 'https://raw.githubusercontent.com/skillmappr/database-init/main/Base-Nacional-Comum-Curricular/relacionamentos/Linguagem-e-suas-Tecnologias/comp-ling-port-para-habilidade-comp.csv' AS line
FIELDTERMINATOR ';'
MERGE (componente:BNCC_Componente {id: line.idComponente})
MERGE (habcomponente:BNCC_HabilidadeDeComponente {id: line.idHabilidadeDeComponente})
MERGE (componente)-[:POSSUI]->(habcomponente)

LOAD CSV WITH HEADERS FROM 'https://raw.githubusercontent.com/skillmappr/database-init/main/Base-Nacional-Comum-Curricular/relacionamentos/Linguagem-e-suas-Tecnologias/comp-ling-port-para-habilidade-comp.csv' AS line
FIELDTERMINATOR ';'
MERGE (componente:BNCC_Componente {id: line.idComponente})
MERGE (habcomponente:BNCC_HabilidadeDeComponente {id: line.idHabilidadeDeComponente})
MERGE (habcomponente)-[:PERTENCE]->(componente)

### Criação do relacionamento entre BNCC_HabilidadeDeComponente e BNCC_CompetenciaEspecifica ###

LOAD CSV WITH HEADERS FROM 'https://raw.githubusercontent.com/skillmappr/database-init/main/Base-Nacional-Comum-Curricular/relacionamentos/Linguagem-e-suas-Tecnologias/habilidade-compon-para-compet-especifica.csv' AS line
FIELDTERMINATOR ';'
MERGE (habcomponente:BNCC_HabilidadeDeComponente {id: line.idHabilidadeDeComponente})
MERGE (compespecifica:BNCC_CompetenciaEspecifica {id: line.idCompetenciaEspecifica})
MERGE (habcomponente)-[:CONTRIBUI_PARA]->(compespecifica)

LOAD CSV WITH HEADERS FROM 'https://raw.githubusercontent.com/skillmappr/database-init/main/Base-Nacional-Comum-Curricular/relacionamentos/Linguagem-e-suas-Tecnologias/habilidade-compon-para-compet-especifica.csv' AS line
FIELDTERMINATOR ';'
MERGE (habcomponente:BNCC_HabilidadeDeComponente {id: line.idHabilidadeDeComponente})
MERGE (compespecifica:BNCC_CompetenciaEspecifica {id: line.idCompetenciaEspecifica})
MERGE (compespecifica)-[:OBTIDA_ATRAVES_DE]->(habcomponente)

### Criação do relacionamento entre BNCC_AreaDeConhecimento e BNCC_Componente ###

LOAD CSV WITH HEADERS FROM 'https://raw.githubusercontent.com/skillmappr/database-init/main/Base-Nacional-Comum-Curricular/relacionamentos/Linguagem-e-suas-Tecnologias/area-ling-tecnologias-para-componentes.csv' AS line
FIELDTERMINATOR ';'
MERGE (area:BNCC_AreaDeConhecimento {id: line.idAreaDeConhecimento})
MERGE (componente:BNCC_Componente {id: line.idComponente})
MERGE (area)-[:POSSUI]->(componente)

LOAD CSV WITH HEADERS FROM 'https://raw.githubusercontent.com/skillmappr/database-init/main/Base-Nacional-Comum-Curricular/relacionamentos/Linguagem-e-suas-Tecnologias/area-ling-tecnologias-para-componentes.csv' AS line
FIELDTERMINATOR ';'
MERGE (area:BNCC_AreaDeConhecimento {id: line.idAreaDeConhecimento})
MERGE (componente:BNCC_Componente {id: line.idComponente})
MERGE (componente)-[:PERTENCE]->(area)

### Criação do relacionamento entre BNCC_AreaDeConhecimento e BNCC_Habilidade ###

LOAD CSV WITH HEADERS FROM 'https://raw.githubusercontent.com/skillmappr/database-init/main/Base-Nacional-Comum-Curricular/relacionamentos/Linguagem-e-suas-Tecnologias/area-ling-tecnologias-para-habilidades.csv' AS line
FIELDTERMINATOR ';'
MERGE (habilidade:BNCC_Habilidade {codigo: line.codigoHabilidade})
MERGE (area:BNCC_AreaDeConhecimento {id: line.idAreaDeConhecimento})
MERGE (habilidade)-[:PERTENCE]->(area)

LOAD CSV WITH HEADERS FROM 'https://raw.githubusercontent.com/skillmappr/database-init/main/Base-Nacional-Comum-Curricular/relacionamentos/Linguagem-e-suas-Tecnologias/area-ling-tecnologias-para-habilidades.csv' AS line
FIELDTERMINATOR ';'
MERGE (habilidade:BNCC_Habilidade {codigo: line.codigoHabilidade})
MERGE (area:BNCC_AreaDeConhecimento {id: line.idAreaDeConhecimento})
MERGE (area)-[:POSSUI]->(habilidade)

### Criação do relacionamento entre BNCC_AreaDeConhecimento e BNCC_CompetenciaEspecifica ###

LOAD CSV WITH HEADERS FROM 'https://raw.githubusercontent.com/skillmappr/database-init/main/Base-Nacional-Comum-Curricular/relacionamentos/Linguagem-e-suas-Tecnologias/area-ling-tecnologias-para-compet-especifica.csv' AS line
FIELDTERMINATOR ';'
MERGE (competencia:BNCC_CompetenciaEspecifica {id: line.idCompetenciaEspecifica})
MERGE (area:BNCC_AreaDeConhecimento {id: line.idAreaDeConhecimento})
MERGE (competencia)-[:PERTENCE]->(area)

LOAD CSV WITH HEADERS FROM 'https://raw.githubusercontent.com/skillmappr/database-init/main/Base-Nacional-Comum-Curricular/relacionamentos/Linguagem-e-suas-Tecnologias/area-ling-tecnologias-para-compet-especifica.csv' AS line
FIELDTERMINATOR ';'
MERGE (competencia:BNCC_CompetenciaEspecifica {id: line.idCompetenciaEspecifica})
MERGE (area:BNCC_AreaDeConhecimento {id: line.idAreaDeConhecimento})
MERGE (area)-[:PERTENCE]->(competencia)

### Criação do relacionamento entre BNCC_CompetenciaEspecifica e BNCC_Habilidade ###

LOAD CSV WITH HEADERS FROM 'https://raw.githubusercontent.com/skillmappr/database-init/main/Base-Nacional-Comum-Curricular/relacionamentos/Linguagem-e-suas-Tecnologias/compet-espec-para-habilidades.csv' AS line
FIELDTERMINATOR ';'
MERGE (habilidade:BNCC_Habilidade {codigo: line.codigoHabilidade})
MERGE (competencia:BNCC_CompetenciaEspecifica {id: line.idCompetencia})
MERGE (habilidade)-[:CONTRIBUI_PARA]->(competencia)

LOAD CSV WITH HEADERS FROM 'https://raw.githubusercontent.com/skillmappr/database-init/main/Base-Nacional-Comum-Curricular/relacionamentos/Linguagem-e-suas-Tecnologias/compet-espec-para-habilidades.csv' AS line
FIELDTERMINATOR ';'
MERGE (habilidade:BNCC_Habilidade {codigo: line.codigoHabilidade})
MERGE (competencia:BNCC_CompetenciaEspecifica {id: line.idCompetencia})
MERGE (competencia)-[:OBTIDA_ATRAVES_DE]->(habilidade)


## Matemática e suas Tecnologias ##

### Criação do relacionamento entre BNCC_Habilidade e BNCC_UnidadeDeEstudo ###

LOAD CSV WITH HEADERS FROM 'https://raw.githubusercontent.com/skillmappr/database-init/main/Base-Nacional-Comum-Curricular/relacionamentos/Matematica-e-suas-Tecnologias/habilidade-mat-tecnologias-para-unid-estudo.csv' AS line
FIELDTERMINATOR ';'
MERGE (habilidade:BNCC_Habilidade {codigo: line.codigoHabilidade})
MERGE (unidestudo:BNCC_UnidadeDeEstudo {id: line.idUnidadeDeEstudo})
MERGE (habilidade)-[:PERTENCE]->(unidestudo)

LOAD CSV WITH HEADERS FROM 'https://raw.githubusercontent.com/skillmappr/database-init/main/Base-Nacional-Comum-Curricular/relacionamentos/Matematica-e-suas-Tecnologias/habilidade-mat-tecnologias-para-unid-estudo.csv' AS line
FIELDTERMINATOR ';'
MERGE (habilidade:BNCC_Habilidade {codigo: line.codigoHabilidade})
MERGE (unidestudo:BNCC_UnidadeDeEstudo {id: line.idUnidadeDeEstudo})
MERGE (unidestudo)-[:POSSUI]->(habilidade)

### Criação do relacionamento entre BNCC_AreaDeConhecimento e BNCC_Habilidade ###

LOAD CSV WITH HEADERS FROM 'https://raw.githubusercontent.com/skillmappr/database-init/main/Base-Nacional-Comum-Curricular/relacionamentos/Matematica-e-suas-Tecnologias/area-mat-tecnologias-para-habilidades.csv' AS line
FIELDTERMINATOR ';'
MERGE (habilidade:BNCC_Habilidade {codigo: line.codigoHabilidade})
MERGE (area:BNCC_AreaDeConhecimento {id: line.idAreaDeConhecimento})
MERGE (habilidade)-[:PERTENCE]->(area)

LOAD CSV WITH HEADERS FROM 'https://raw.githubusercontent.com/skillmappr/database-init/main/Base-Nacional-Comum-Curricular/relacionamentos/Matematica-e-suas-Tecnologias/area-mat-tecnologias-para-habilidades.csv' AS line
FIELDTERMINATOR ';'
MERGE (habilidade:BNCC_Habilidade {codigo: line.codigoHabilidade})
MERGE (area:BNCC_AreaDeConhecimento {id: line.idAreaDeConhecimento})
MERGE (area)-[:POSSUI]->(habilidade)

### Criação do relacionamento entre BNCC_AreaDeConhecimento e BNCC_CompetenciaEspecifica ###

LOAD CSV WITH HEADERS FROM 'https://raw.githubusercontent.com/skillmappr/database-init/main/Base-Nacional-Comum-Curricular/relacionamentos/Matematica-e-suas-Tecnologias/area-mat-tecnologias-para-compet-especifica.csv' AS line
FIELDTERMINATOR ';'
MERGE (competencia:BNCC_CompetenciaEspecifica {id: line.idCompetenciaEspecifica})
MERGE (area:BNCC_AreaDeConhecimento {id: line.idAreaDeConhecimento})
MERGE (competencia)-[:PERTENCE]->(area)

LOAD CSV WITH HEADERS FROM 'https://raw.githubusercontent.com/skillmappr/database-init/main/Base-Nacional-Comum-Curricular/relacionamentos/Matematica-e-suas-Tecnologias/area-mat-tecnologias-para-compet-especifica.csv' AS line
FIELDTERMINATOR ';'
MERGE (competencia:BNCC_CompetenciaEspecifica {id: line.idCompetenciaEspecifica})
MERGE (area:BNCC_AreaDeConhecimento {id: line.idAreaDeConhecimento})
MERGE (area)-[:PERTENCE]->(competencia)

### Criação do relacionamento entre BNCC_CompetenciaEspecifica e BNCC_Habilidade ###

LOAD CSV WITH HEADERS FROM 'https://raw.githubusercontent.com/skillmappr/database-init/main/Base-Nacional-Comum-Curricular/relacionamentos/Matematica-e-suas-Tecnologias/compet-espec-para-habilidades.csv' AS line
FIELDTERMINATOR ';'
MERGE (habilidade:BNCC_Habilidade {codigo: line.codigoHabilidade})
MERGE (competencia:BNCC_CompetenciaEspecifica {id: line.idCompetencia})
MERGE (habilidade)-[:CONTRIBUI_PARA]->(competencia)

LOAD CSV WITH HEADERS FROM 'https://raw.githubusercontent.com/skillmappr/database-init/main/Base-Nacional-Comum-Curricular/relacionamentos/Matematica-e-suas-Tecnologias/compet-espec-para-habilidades.csv' AS line
FIELDTERMINATOR ';'
MERGE (habilidade:BNCC_Habilidade {codigo: line.codigoHabilidade})
MERGE (competencia:BNCC_CompetenciaEspecifica {id: line.idCompetencia})
MERGE (competencia)-[:OBTIDA_ATRAVES_DE]->(habilidade)


## Ciencias da Natureza e suas Tecnologias ##

### Criação do relacionamento entre BNCC_AreaDeConhecimento e BNCC_Componente ###

LOAD CSV WITH HEADERS FROM 'https://raw.githubusercontent.com/skillmappr/database-init/main/Base-Nacional-Comum-Curricular/relacionamentos/Ciencias-da-Natureza-e-suas-Tecnologias/area-cien-nat-tecnologias-para-componentes.csv' AS line
FIELDTERMINATOR ';'
MERGE (area:BNCC_AreaDeConhecimento {id: line.idAreaDeConhecimento})
MERGE (componente:BNCC_Componente {id: line.idComponente})
MERGE (area)-[:POSSUI]->(componente)

LOAD CSV WITH HEADERS FROM 'https://raw.githubusercontent.com/skillmappr/database-init/main/Base-Nacional-Comum-Curricular/relacionamentos/Ciencias-da-Natureza-e-suas-Tecnologias/area-cien-nat-tecnologias-para-componentes.csv' AS line
FIELDTERMINATOR ';'
MERGE (area:BNCC_AreaDeConhecimento {id: line.idAreaDeConhecimento})
MERGE (componente:BNCC_Componente {id: line.idComponente})
MERGE (componente)-[:PERTENCE]->(area)

### Criação do relacionamento entre BNCC_AreaDeConhecimento e BNCC_Habilidade ###

LOAD CSV WITH HEADERS FROM 'https://raw.githubusercontent.com/skillmappr/database-init/main/Base-Nacional-Comum-Curricular/relacionamentos/Ciencias-da-Natureza-e-suas-Tecnologias/area-cien-nat-tecnologias-para-habilidades.csv' AS line
FIELDTERMINATOR ';'
MERGE (habilidade:BNCC_Habilidade {codigo: line.codigoHabilidade})
MERGE (area:BNCC_AreaDeConhecimento {id: line.idAreaDeConhecimento})
MERGE (habilidade)-[:PERTENCE]->(area)

LOAD CSV WITH HEADERS FROM 'https://raw.githubusercontent.com/skillmappr/database-init/main/Base-Nacional-Comum-Curricular/relacionamentos/Ciencias-da-Natureza-e-suas-Tecnologias/area-cien-nat-tecnologias-para-habilidades.csv' AS line
FIELDTERMINATOR ';'
MERGE (habilidade:BNCC_Habilidade {codigo: line.codigoHabilidade})
MERGE (area:BNCC_AreaDeConhecimento {id: line.idAreaDeConhecimento})
MERGE (area)-[:POSSUI]->(habilidade)

### Criação do relacionamento entre BNCC_AreaDeConhecimento e BNCC_CompetenciaEspecifica ###

LOAD CSV WITH HEADERS FROM 'https://raw.githubusercontent.com/skillmappr/database-init/main/Base-Nacional-Comum-Curricular/relacionamentos/Ciencias-da-Natureza-e-suas-Tecnologias/area-cien-nat-tecnologias-para-compet-especifica.csv' AS line
FIELDTERMINATOR ';'
MERGE (competencia:BNCC_CompetenciaEspecifica {id: line.idCompetenciaEspecifica})
MERGE (area:BNCC_AreaDeConhecimento {id: line.idAreaDeConhecimento})
MERGE (competencia)-[:PERTENCE]->(area)

LOAD CSV WITH HEADERS FROM 'https://raw.githubusercontent.com/skillmappr/database-init/main/Base-Nacional-Comum-Curricular/relacionamentos/Ciencias-da-Natureza-e-suas-Tecnologias/area-cien-nat-tecnologias-para-compet-especifica.csv' AS line
FIELDTERMINATOR ';'
MERGE (competencia:BNCC_CompetenciaEspecifica {id: line.idCompetenciaEspecifica})
MERGE (area:BNCC_AreaDeConhecimento {id: line.idAreaDeConhecimento})
MERGE (area)-[:PERTENCE]->(competencia)

### Criação do relacionamento entre BNCC_CompetenciaEspecifica e BNCC_Habilidade ###

LOAD CSV WITH HEADERS FROM 'https://raw.githubusercontent.com/skillmappr/database-init/main/Base-Nacional-Comum-Curricular/relacionamentos/Ciencias-da-Natureza-e-suas-Tecnologias/compet-espec-para-habilidades.csv' AS line
FIELDTERMINATOR ';'
MERGE (habilidade:BNCC_Habilidade {codigo: line.codigoHabilidade})
MERGE (competencia:BNCC_CompetenciaEspecifica {id: line.idCompetencia})
MERGE (habilidade)-[:CONTRIBUI_PARA]->(competencia)

LOAD CSV WITH HEADERS FROM 'https://raw.githubusercontent.com/skillmappr/database-init/main/Base-Nacional-Comum-Curricular/relacionamentos/Ciencias-da-Natureza-e-suas-Tecnologias/compet-espec-para-habilidades.csv' AS line
FIELDTERMINATOR ';'
MERGE (habilidade:BNCC_Habilidade {codigo: line.codigoHabilidade})
MERGE (competencia:BNCC_CompetenciaEspecifica {id: line.idCompetencia})
MERGE (competencia)-[:OBTIDA_ATRAVES_DE]->(habilidade)


## Ciencias Humanas e Sociais Aplicadas ##

### Criação do relacionamento entre BNCC_AreaDeConhecimento e BNCC_Componente ###

LOAD CSV WITH HEADERS FROM 'https://raw.githubusercontent.com/skillmappr/database-init/main/Base-Nacional-Comum-Curricular/relacionamentos/Ciencias-Humanas-e-Sociais-Aplicadas/area-cien-hum-sociais-para-componentes.csv' AS line
FIELDTERMINATOR ';'
MERGE (area:BNCC_AreaDeConhecimento {id: line.idAreaDeConhecimento})
MERGE (componente:BNCC_Componente {id: line.idComponente})
MERGE (area)-[:POSSUI]->(componente)

LOAD CSV WITH HEADERS FROM 'https://raw.githubusercontent.com/skillmappr/database-init/main/Base-Nacional-Comum-Curricular/relacionamentos/Ciencias-Humanas-e-Sociais-Aplicadas/area-cien-hum-sociais-para-componentes.csv' AS line
FIELDTERMINATOR ';'
MERGE (area:BNCC_AreaDeConhecimento {id: line.idAreaDeConhecimento})
MERGE (componente:BNCC_Componente {id: line.idComponente})
MERGE (componente)-[:PERTENCE]->(area)

### Criação do relacionamento entre BNCC_AreaDeConhecimento e BNCC_Habilidade ###

LOAD CSV WITH HEADERS FROM 'https://raw.githubusercontent.com/skillmappr/database-init/main/Base-Nacional-Comum-Curricular/relacionamentos/Ciencias-Humanas-e-Sociais-Aplicadas/area-cien-hum-sociais-para-habilidades.csv' AS line
FIELDTERMINATOR ';'
MERGE (habilidade:BNCC_Habilidade {codigo: line.codigoHabilidade})
MERGE (area:BNCC_AreaDeConhecimento {id: line.idAreaDeConhecimento})
MERGE (habilidade)-[:PERTENCE]->(area)

LOAD CSV WITH HEADERS FROM 'https://raw.githubusercontent.com/skillmappr/database-init/main/Base-Nacional-Comum-Curricular/relacionamentos/Ciencias-Humanas-e-Sociais-Aplicadas/area-cien-hum-sociais-para-habilidades.csv' AS line
FIELDTERMINATOR ';'
MERGE (habilidade:BNCC_Habilidade {codigo: line.codigoHabilidade})
MERGE (area:BNCC_AreaDeConhecimento {id: line.idAreaDeConhecimento})
MERGE (area)-[:POSSUI]->(habilidade)

### Criação do relacionamento entre BNCC_AreaDeConhecimento e BNCC_CompetenciaEspecifica ###

LOAD CSV WITH HEADERS FROM 'https://raw.githubusercontent.com/skillmappr/database-init/main/Base-Nacional-Comum-Curricular/relacionamentos/Ciencias-Humanas-e-Sociais-Aplicadas/area-cien-hum-sociais-para-compet-especifica.csv' AS line
FIELDTERMINATOR ';'
MERGE (competencia:BNCC_CompetenciaEspecifica {id: line.idCompetenciaEspecifica})
MERGE (area:BNCC_AreaDeConhecimento {id: line.idAreaDeConhecimento})
MERGE (competencia)-[:PERTENCE]->(area)

LOAD CSV WITH HEADERS FROM 'https://raw.githubusercontent.com/skillmappr/database-init/main/Base-Nacional-Comum-Curricular/relacionamentos/Ciencias-Humanas-e-Sociais-Aplicadas/area-cien-hum-sociais-para-compet-especifica.csv' AS line
FIELDTERMINATOR ';'
MERGE (competencia:BNCC_CompetenciaEspecifica {id: line.idCompetenciaEspecifica})
MERGE (area:BNCC_AreaDeConhecimento {id: line.idAreaDeConhecimento})
MERGE (area)-[:PERTENCE]->(competencia)

### Criação do relacionamento entre BNCC_CompetenciaEspecifica e BNCC_Habilidade ###

LOAD CSV WITH HEADERS FROM 'https://raw.githubusercontent.com/skillmappr/database-init/main/Base-Nacional-Comum-Curricular/relacionamentos/Ciencias-Humanas-e-Sociais-Aplicadas/compet-espec-para-habilidades.csv' AS line
FIELDTERMINATOR ';'
MERGE (habilidade:BNCC_Habilidade {codigo: line.codigoHabilidade})
MERGE (competencia:BNCC_CompetenciaEspecifica {id: line.idCompetencia})
MERGE (habilidade)-[:CONTRIBUI_PARA]->(competencia)

LOAD CSV WITH HEADERS FROM 'https://raw.githubusercontent.com/skillmappr/database-init/main/Base-Nacional-Comum-Curricular/relacionamentos/Ciencias-Humanas-e-Sociais-Aplicadas/compet-espec-para-habilidades.csv' AS line
FIELDTERMINATOR ';'
MERGE (habilidade:BNCC_Habilidade {codigo: line.codigoHabilidade})
MERGE (competencia:BNCC_CompetenciaEspecifica {id: line.idCompetencia})
MERGE (competencia)-[:OBTIDA_ATRAVES_DE]->(habilidade)