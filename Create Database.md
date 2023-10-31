Guia para criação da base de dados

# ETAPA REFERENCIAIS SOCIEDADE BRASILEIRA DA COMPUTAÇÃO #

## CRIAÇÃO DOS NÓS ##

### Criação de nós do tipo SBC_Eixo ###

LOAD CSV WITH HEADERS FROM 'https://raw.githubusercontent.com/skillmappr/database-init/main/Sociedade%20Brasileira%20da%20Computação/nós/sbc-eixos.csv' as line
FIELDTERMINATOR ';'
CREATE (:Eixo{id:line.id, tipo: line.tipo, nome: line.nome, competenciaGeral: line.competenciaGeral})

MATCH (n {id:'1'}) SET n.eixo = 'Fudamentos de Sistemas de Computação' RETURN n
MATCH (n {id:'2'}) SET n.eixo = 'Desenvolvimento de Sistemas Computacionais' RETURN n
MATCH (n {id:'3'}) SET n.eixo = 'Gerenciamento de Sistemas Computacionais' RETURN n
MATCH (n {id:'4'}) SET n.eixo = 'Inovação e Empreendedorismo' RETURN n
MATCH (n {id:'5'}) SET n.eixo = 'Desenvolvimento Pessoal e Profissional' RETURN n

### Criação de nós do tipo SBC_Conteudo ###

LOAD CSV WITH HEADERS FROM 'https://raw.githubusercontent.com/skillmappr/database-init/main/Sociedade%20Brasileira%20da%20Computação/nós/sbc-conteudos.csv' AS line
FIELDTERMINATOR ';'
CREATE (:Conteudo{id:line.id, tipo: line.tipo, nome: line.nome})

### Criação de nós do tipo SBC_CompetenciaDerivada ###

LOAD CSV WITH HEADERS FROM 'https://raw.githubusercontent.com/skillmappr/database-init/main/Sociedade%20Brasileira%20da%20Computação/nós/sbc-competencias-derivadas.csv' AS line
FIELDTERMINATOR ';'
CREATE (:CompetenciaDerivada{id:line.id, tipo: line.tipo, codigo: line.codigo, descricao:line.descricao})

### Criação de nós do tipo SBC_CompetenciaGeralEgresso ###

LOAD CSV WITH HEADERS FROM 'https://raw.githubusercontent.com/skillmappr/database-init/main/Sociedade%20Brasileira%20da%20Computação/nós/sbc-competencias-gerais-egressos.csv' AS line
FIELDTERMINATOR ';'
CREATE (:CompetenciaGeralEgresso{id:line.id, tipo: line.tipo, descricao: line.descricao})

### Criação de nós do tipo SBC_CompetenciaEspecificaEgresso ###

LOAD CSV WITH HEADERS FROM 'https://raw.githubusercontent.com/skillmappr/database-init/main/Sociedade%20Brasileira%20da%20Computação/nós/sbc-competencias-especificas-egressos.csv' AS line
FIELDTERMINATOR ';'
CREATE (:CompetenciaEspecificaEgresso{id:line.id, tipo: line.tipo, descricao: line.descricao})


## CRIAÇÃO DOS RELACIONAMENTOS ENTRE OS NÓS ##

### Criação do relacionamento entre SBC_Conteudo e SBC_CompetenciaDerivada ###

LOAD CSV WITH HEADERS FROM 'https://raw.githubusercontent.com/skillmappr/database-init/main/Sociedade%20Brasileira%20da%20Computação/relacionamentos/conteudo-para-competencia-derivada.csv' AS line
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

LOAD CSV WITH HEADERS FROM 'https://raw.githubusercontent.com/skillmappr/database-init/main/Sociedade%20Brasileira%20da%20Computação/relacionamentos/conteudo-para-eixo.csv' AS line
FIELDTERMINATOR ';'
MERGE (cont:Conteudo {id: line.idConteudo})
MERGE (eixo:Eixo {id: line.idEixo})
MERGE (cont)-[:PERTENCE]->(eixo)

LOAD CSV WITH HEADERS FROM 'https://raw.githubusercontent.com/skillmappr/database-init/main/Sociedade%20Brasileira%20da%20Computação/relacionamentos/conteudo-para-eixo.csv' AS line
FIELDTERMINATOR ';'
MERGE (cont:Conteudo {id: line.idConteudo})
MERGE (eixo:Eixo {id: line.idEixo})
MERGE (eixo)-[:POSSUI]->(cont)

### Criação do relacionamento entre SBC_Eixo e SBC_CompetenciaDerivada ###

LOAD CSV WITH HEADERS FROM 'https://raw.githubusercontent.com/skillmappr/database-init/main/Sociedade%20Brasileira%20da%20Computação/relacionamentos/eixo-para-competencia-derivada.csv' AS line
FIELDTERMINATOR ';'
MERGE (eixo:Eixo {id: line.idEixo})
MERGE (comp:CompetenciaDerivada {id: line.idCompetenciaDerivada})
MERGE (comp)-[:PERTENCE]->(eixo)

LOAD CSV WITH HEADERS FROM 'https://raw.githubusercontent.com/skillmappr/database-init/main/Sociedade%20Brasileira%20da%20Computação/relacionamentos/eixo-para-competencia-derivada.csv' AS line
FIELDTERMINATOR ';'
MERGE (eixo:Eixo {id: line.idEixo})
MERGE (comp:CompetenciaDerivada {id: line.idCompetenciaDerivada})
MERGE (eixo)-[:POSSUI]->(comp)

### Criação do relacionamento entre SBC_CompetenciaGeralEgresso e SBC_CompetenciaDerivada ###

LOAD CSV WITH HEADERS FROM 'https://raw.githubusercontent.com/skillmappr/database-init/main/Sociedade%20Brasileira%20da%20Computação/relacionamentos/competencia-geral-egressos-para-competencia-derivada.csv' AS line
FIELDTERMINATOR ';'
MERGE (compGeralEgresso:CompetenciaGeralEgresso {id: line.idCompetenciaGeralEgresso})
MERGE (compDerivada:CompetenciaDerivada {id: line.idCompetenciaDerivada})
MERGE (compDerivada)-[:CONTRIBUI_PARA]->(compGeralEgresso)

LOAD CSV WITH HEADERS FROM 'https://raw.githubusercontent.com/skillmappr/database-init/main/Sociedade%20Brasileira%20da%20Computação/relacionamentos/competencia-geral-egressos-para-competencia-derivada.csv' AS line
FIELDTERMINATOR ';'
MERGE (compGeralEgresso:CompetenciaGeralEgresso {id: line.idCompetenciaGeralEgresso})
MERGE (compDerivada:CompetenciaDerivada {id: line.idCompetenciaDerivada})
MERGE (compGeralEgresso)-[:OBTIDA_ATRAVES_DE]->(compDerivada)

### Criação do relacionamento entre SBC_CompetenciaEspecificaEgresso e SBC_CompetenciaDerivada ###

LOAD CSV WITH HEADERS FROM 'https://raw.githubusercontent.com/skillmappr/database-init/main/Sociedade%20Brasileira%20da%20Computação/relacionamentos/competencia-especifica-egressos-para-competencia-derivada.csv' AS line
FIELDTERMINATOR ';'
MERGE (compEspecificaEgresso:CompetenciaEspecificaEgresso {id: line.idCompetenciaEspecificaEgresso})
MERGE (compDerivada:CompetenciaDerivada {id: line.idCompetenciaDerivada})
MERGE (compDerivada)-[:CONTRIBUI_PARA]->(compEspecificaEgresso)

LOAD CSV WITH HEADERS FROM 'https://raw.githubusercontent.com/skillmappr/database-init/main/Sociedade%20Brasileira%20da%20Computação/relacionamentos/competencia-especifica-egressos-para-competencia-derivada.csv' AS line
FIELDTERMINATOR ';'
MERGE (compEspecificaEgresso:CompetenciaEspecificaEgresso {id: line.idCompetenciaEspecificaEgresso})
MERGE (compDerivada:CompetenciaDerivada {id: line.idCompetenciaDerivada})
MERGE (compEspecificaEgresso)-[:OBTIDA_ATRAVES_DE]->(compDerivada)


# ETAPA REFERENCIAIS BASE NACIONAL COMUM CURRICULAR #

## CRIAÇÃO DOS NÓS ##

### Criação de nós do tipo BNCC_AreaDeConhecimento ###

LOAD CSV WITH HEADERS FROM 'https://raw.githubusercontent.com/skillmappr/database-init/main/Base%20Nacional%20Comum%20Curricular/nós/bncc-areas-do-conhecimento.csv' AS line
FIELDTERMINATOR ';'
CREATE (:BNCC_AreaDeConhecimento{id:line.id, tipo: line.tipo, nome: line.nome})

### Criação de nós do tipo BNCC_CompetenciaGeral ###

LOAD CSV WITH HEADERS FROM 'https://raw.githubusercontent.com/skillmappr/database-init/main/Base%20Nacional%20Comum%20Curricular/nós/bncc-competencias-gerais.csv' AS line
FIELDTERMINATOR ';'
CREATE (:BNCC_CompetenciaGeral{id:line.id, tipo: line.tipo, descricao: line.descricao})

### Criação de nós do tipo BNCC_CompetenciaEspecifica (Matemática e suas Tecnologias)###

LOAD CSV WITH HEADERS FROM 'https://raw.githubusercontent.com/skillmappr/database-init/main/Base%20Nacional%20Comum%20Curricular/nós/Matemática%20e%20suas%20Tecnologias/bncc-competencias-especificas-mat-tecnologias.csv' AS line
FIELDTERMINATOR ';'
CREATE (:BNCC_CompetenciaEspecifica{id:line.id, tipo: line.tipo, descricao: line.descricao})

### Criação de nós do tipo BNCC_Habilidade (Matemática e suas Tecnologias)###

LOAD CSV WITH HEADERS FROM 'https://raw.githubusercontent.com/skillmappr/database-init/main/Base%20Nacional%20Comum%20Curricular/nós/Matemática%20e%20suas%20Tecnologias/bncc-habilidades-mat-tecnologias.csv' AS line
FIELDTERMINATOR ';'
CREATE (:BNCC_Habilidade{id:line.id, tipo: line.tipo, codigo: line.codigo, descricao: line.descricao})

### Criação de nós do tipo BNCC_UnidadeDeEstudo (Matemática e suas Tecnologias)###

LOAD CSV WITH HEADERS FROM 'https://raw.githubusercontent.com/skillmappr/database-init/main/Base%20Nacional%20Comum%20Curricular/nós/Matemática%20e%20suas%20Tecnologias/bncc-unid-estudo-mat-tecnologias.csv' AS line
FIELDTERMINATOR ';'
CREATE (:BNCC_UnidadeDeEstudo{id:line.id, tipo: line.tipo, nome: line.nome})

### Criação de nós do tipo BNCC_CompetenciaEspecifica (Linguagem e suas Tecnologias)###

LOAD CSV WITH HEADERS FROM 'https://raw.githubusercontent.com/skillmappr/database-init/main/Base%20Nacional%20Comum%20Curricular/nós/Linguagem%20e%20suas%20Tecnologias/bncc-competencias-especificas-ling-tecnologias.csv' AS line
FIELDTERMINATOR ';'
CREATE (:BNCC_CompetenciaEspecifica{id:line.id, tipo: line.tipo, descricao: line.descricao})

### Criação de nós do tipo BNCC_Habilidade (Linguagem e suas Tecnologias)###

LOAD CSV WITH HEADERS FROM 'https://raw.githubusercontent.com/skillmappr/database-init/main/Base%20Nacional%20Comum%20Curricular/nós/Linguagem%20e%20suas%20Tecnologias/bncc-habilidades-ling-tecnologias.csv' AS line
FIELDTERMINATOR ';'
CREATE (:BNCC_Habilidade{id:line.id, tipo: line.tipo, codigo: line.codigo, descricao: line.descricao})

### Criação de nós do tipo BNCC_Componente (Linguagem e suas Tecnologias)###

LOAD CSV WITH HEADERS FROM 'https://raw.githubusercontent.com/skillmappr/database-init/main/Base%20Nacional%20Comum%20Curricular/nós/Linguagem%20e%20suas%20Tecnologias/bncc-componentes-ling-tecnologias.csv' AS line
FIELDTERMINATOR ';'
CREATE (:BNCC_Componente{id:line.id, tipo: line.tipo, nome: line.nome})

### Criação de nós do tipo BNCC_HabilidadeDeComponente (Linguagem e suas Tecnologias)###

LOAD CSV WITH HEADERS FROM 'https://raw.githubusercontent.com/skillmappr/database-init/main/Base%20Nacional%20Comum%20Curricular/nós/Linguagem%20e%20suas%20Tecnologias/bncc-habilidades-compon-ling-portug.csv' AS line
FIELDTERMINATOR ';'
CREATE (:BNCC_HabilidadeDeComponente{id:line.id, tipo: line.tipo, codigo: line.codigo, descricao: line.descricao})

### Criação de nós do tipo BNCC_CompetenciaEspecifica (Ciências da Natureza e suas Tecnologias)###

LOAD CSV WITH HEADERS FROM 'https://raw.githubusercontent.com/skillmappr/database-init/main/Base%20Nacional%20Comum%20Curricular/nós/Ciências%20da%20Natureza%20e%20suas%20Tecnologias/bncc-competencias-especificas-cien-nat-tecnologias.csv' AS line
FIELDTERMINATOR ';'
CREATE (:BNCC_CompetenciaEspecifica{id:line.id, tipo: line.tipo, descricao: line.descricao})

### Criação de nós do tipo BNCC_Habilidade (Ciências da Natureza e suas Tecnologias)###

LOAD CSV WITH HEADERS FROM 'https://raw.githubusercontent.com/skillmappr/database-init/main/Base%20Nacional%20Comum%20Curricular/nós/Ciências%20da%20Natureza%20e%20suas%20Tecnologias/bncc-habilidades-cien-nat-tecnologias.csv' AS line
FIELDTERMINATOR ';'
CREATE (:BNCC_Habilidade{id:line.id, tipo: line.tipo, codigo: line.codigo, descricao: line.descricao})

### Criação de nós do tipo BNCC_Componente (Ciências da Natureza e suas Tecnologias)###

LOAD CSV WITH HEADERS FROM 'https://raw.githubusercontent.com/skillmappr/database-init/main/Base%20Nacional%20Comum%20Curricular/nós/Ciências%20da%20Natureza%20e%20suas%20Tecnologias/bncc-componentes-cien-nat-tecnologias.csv' AS line
FIELDTERMINATOR ';'
CREATE (:BNCC_Componente{id:line.id, tipo: line.tipo, nome: line.nome})

### Criação de nós do tipo BNCC_CompetenciaEspecifica (Ciências Humanas e Sociais Aplicadas e suas Tecnologias)###

LOAD CSV WITH HEADERS FROM 'https://raw.githubusercontent.com/skillmappr/database-init/main/Base%20Nacional%20Comum%20Curricular/nós/Ciências%20Humanas%20e%20Sociais%20Aplicadas/bncc-competencias-especificas-cien-hum-sociais.csv' AS line
FIELDTERMINATOR ';'
CREATE (:BNCC_CompetenciaEspecifica{id:line.id, tipo: line.tipo, descricao: line.descricao})

### Criação de nós do tipo BNCC_Habilidade (Ciências Humanas e Sociais Aplicadas e suas Tecnologias)###

LOAD CSV WITH HEADERS FROM 'https://raw.githubusercontent.com/skillmappr/database-init/main/Base%20Nacional%20Comum%20Curricular/nós/Ciências%20Humanas%20e%20Sociais%20Aplicadas/bncc-habilidades-cien-hum-sociais.csv' AS line
FIELDTERMINATOR ';'
CREATE (:BNCC_Habilidade{id:line.id, tipo: line.tipo, codigo: line.codigo, descricao: line.descricao})

### Criação de nós do tipo BNCC_Componente (Ciências Humanas e Sociais Aplicadas e suas Tecnologias)###

LOAD CSV WITH HEADERS FROM 'https://raw.githubusercontent.com/skillmappr/database-init/main/Base%20Nacional%20Comum%20Curricular/nós/Ciências%20Humanas%20e%20Sociais%20Aplicadas/bncc-componentes-cien-hum-sociais.csv' AS line
FIELDTERMINATOR ';'
CREATE (:BNCC_Componente{id:line.id, tipo: line.tipo, nome: line.nome})