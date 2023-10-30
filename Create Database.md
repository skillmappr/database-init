Guia para criação da base de dados

# ETAPA REFERENCIAIS SOCIEDADE BRASILEIRA DA COMPUTAÇÃO #

## CRIAÇÃO DOS NÓS ##

### Criação de nós do tipo SBC_Conteudo ###

LOAD CSV WITH HEADERS FROM 'https://raw.githubusercontent.com/skillmappr/database-init/main/nós/conteudos.csv' AS line
FIELDTERMINATOR ';'
CREATE (:Conteudo{id:line.id, nome: line.nome})


### Criação de nós do tipo DBC_CompetenciaDerivada ###

LOAD CSV WITH HEADERS FROM 'https://raw.githubusercontent.com/skillmappr/database-init/main/nós/competencias-derivadas.csv' AS line
FIELDTERMINATOR ';'
CREATE (:CompetenciaDerivada{id:line.id, nome: line.nome, descricao:line.descricao})


### Criação de nós do tipo SBC_EIXO ###

LOAD CSV WITH HEADERS FROM 'https://raw.githubusercontent.com/skillmappr/database-init/main/nós/eixos.csv' as line
FIELDTERMINATOR ';'
CREATE (:Eixo{id:line.id, nome: line.nome, competenciaGeral: line.competenciaGeral})

MATCH (n {id:'1'}) SET n.eixo = 'Fudamentos de Sistemas de Computação' RETURN n
MATCH (n {id:'2'}) SET n.eixo = 'Desenvolvimento de Sistemas Computacionais' RETURN n
MATCH (n {id:'3'}) SET n.eixo = 'Gerenciamento de Sistemas Computacionais' RETURN n
MATCH (n {id:'4'}) SET n.eixo = 'Inovação e Empreendedorismo' RETURN n
MATCH (n {id:'5'}) SET n.eixo = 'Desenvolvimento Pessoal e Profissional' RETURN n


### Criação de nós do tipo SBC_CompetenciaGeralEgresso ###

LOAD CSV WITH HEADERS FROM 'https://raw.githubusercontent.com/skillmappr/database-init/main/nós/competencias-gerais-egressos.csv' AS line
FIELDTERMINATOR ';'
CREATE (:CompetenciaGeralEgresso{id:line.id, descricao: line.descricao})


CREATE INDEX FOR (compGeralEgresso:CompetenciaGeralEgresso) ON compGeralEgresso.id


### Criação de nós do tipo SBC_CompetenciaEspecificaEgresso ###

LOAD CSV WITH HEADERS FROM 'https://raw.githubusercontent.com/skillmappr/database-init/main/nós/competencias-especificas-egressos.csv' AS line
FIELDTERMINATOR ';'
CREATE (:CompetenciaEspecificaEgresso{id:line.id, descricao: line.descricao})

CREATE INDEX FOR (compEspecificaEgresso:CompetenciaEspecificaEgresso) ON compEspecificaEgresso.id



## CRIAÇÃO DOS RELACIONAMENTOS ENTRE OS NÓS ##


### Criação do relacionamento entre SBC_Conteudo e SBC_CompetenciaDerivada ###

LOAD CSV WITH HEADERS FROM 'https://raw.githubusercontent.com/skillmappr/database-init/main/relacionamentos/conteudo-para-competencia-derivada.csv' AS line
FIELDTERMINATOR ';'
MERGE (cont:Conteudo {id: line.idConteudo})
MERGE (comp:CompetenciaDerivada {id: line.idCompetenciaDerivada})

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

LOAD CSV WITH HEADERS FROM 'https://raw.githubusercontent.com/skillmappr/database-init/main/relacionamentos/conteudo-para-eixo.csv' AS line
FIELDTERMINATOR ';'
MERGE (cont:Conteudo {id: line.idConteudo})
MERGE (eixo:Eixo {id: line.idEixo})
MERGE (cont)-[:PERTENCE]->(eixo)

LOAD CSV WITH HEADERS FROM 'https://raw.githubusercontent.com/skillmappr/database-init/main/relacionamentos/conteudo-para-eixo.csv' AS line
FIELDTERMINATOR ';'
MERGE (cont:Conteudo {id: line.idConteudo})
MERGE (eixo:Eixo {id: line.idEixo})
MERGE (eixo)-[:POSSUI]->(cont)


### Criação do relacionamento entre SBC_Eixo e SBC_CompetenciaDerivada ###

LOAD CSV WITH HEADERS FROM 'https://raw.githubusercontent.com/skillmappr/database-init/main/relacionamentos/eixo-para-competencia-derivada.csv' AS line
FIELDTERMINATOR ';'
MERGE (eixo:Eixo {id: line.idEixo})
MERGE (comp:CompetenciaDerivada {id: line.idCompetenciaDerivada})
MERGE (comp)-[:PERTENCE]->(eixo)

LOAD CSV WITH HEADERS FROM 'https://raw.githubusercontent.com/skillmappr/database-init/main/relacionamentos/eixo-para-competencia-derivada.csv' AS line
FIELDTERMINATOR ';'
MERGE (eixo:Eixo {id: line.idEixo})
MERGE (comp:CompetenciaDerivada {id: line.idCompetenciaDerivada})
MERGE (eixo)-[:POSSUI]->(comp)


### Criação do relacionamento entre SBC_CompetenciaGeralEgresso e SBC_CompetenciaDerivada ###

LOAD CSV WITH HEADERS FROM 'https://raw.githubusercontent.com/skillmappr/database-init/main/relacionamentos/competencia-geral-egressos-para-competencia-derivada.csv' AS line
FIELDTERMINATOR ';'
MERGE (compGeralEgresso:CompetenciaGeralEgresso {id: line.idCompetenciaGeralEgresso})
MERGE (compDerivada:CompetenciaDerivada {id: line.idCompetenciaDerivada})
MERGE (compDerivada)-[:CONTRIBUI_PARA]->(compGeralEgresso)

LOAD CSV WITH HEADERS FROM 'https://raw.githubusercontent.com/skillmappr/database-init/main/relacionamentos/competencia-geral-egressos-para-competencia-derivada.csv' AS line
FIELDTERMINATOR ';'
MERGE (compGeralEgresso:CompetenciaGeralEgresso {id: line.idCompetenciaGeralEgresso})
MERGE (compDerivada:CompetenciaDerivada {id: line.idCompetenciaDerivada})
MERGE (compGeralEgresso)-[:OBTIDA_ATRAVES_DE]->(compDerivada)



### Criação do relacionamento entre SBC_CompetenciaEspecificaEgresso e SBC_CompetenciaDerivada ###

LOAD CSV WITH HEADERS FROM 'https://raw.githubusercontent.com/skillmappr/database-init/main/relacionamentos/competencia-especifica-egressos-para-competencia-derivada.csv' AS line
FIELDTERMINATOR ';'
MERGE (compEspecificaEgresso:CompetenciaEspecificaEgresso {id: line.idCompetenciaEspecificaEgresso})
MERGE (compDerivada:CompetenciaDerivada {id: line.idCompetenciaDerivada})
MERGE (compDerivada)-[:CONTRIBUI_PARA]->(compEspecificaEgresso)

LOAD CSV WITH HEADERS FROM 'https://raw.githubusercontent.com/skillmappr/database-init/main/relacionamentos/competencia-especifica-egressos-para-competencia-derivada.csv' AS line
FIELDTERMINATOR ';'
MERGE (compEspecificaEgresso:CompetenciaEspecificaEgresso {id: line.idCompetenciaEspecificaEgresso})
MERGE (compDerivada:CompetenciaDerivada {id: line.idCompetenciaDerivada})
MERGE (compEspecificaEgresso)-[:OBTIDA_ATRAVES_DE]->(compDerivada)


