## Tarefa de Cypher e o FDA Adverse Event Reporting System (FAERS)

## Exercício 1

Escreva uma sentença em Cypher que crie o medicamento de nome `Metamizole`, código no DrugBank `DB04817`.

### Resolução
~~~cypher
CREATE (:Drug {drugbank: "DB04817", name: "Metamizole"})
~~~

## Exercício 2

Considerando que a `Dipyrone` e `Metamizole` são o mesmo medicamento com nomes diferentes, crie uma aresta com o rótulo `:SameAs` que ligue os dois.

### Resolução
~~~cypher
MATCH (drug1:Drug {name:"Dipyrone"})
MATCH (drug2:Drug {name:"Metamizole"})
CREATE (drug1)-[:SameAs]->(drug2)
~~~

## Exercício 3

Use o `DELETE` para excluir o relacionamento que você criou (apenas ele).

### Resolução
~~~cypher
MATCH (:Drug {drugbank:"DB04817"})-[rel:SameAs]->(:Drug {drugbank:"DB04817"})
DELETE rel
~~~

## Exercício 4

Faça a projeção em relação a Patologia, ou seja, conecte patologias que são tratadas pela mesma droga.

### Resolução
~~~cypher
MATCH (pat1:Pathology)<-[a]-(d:Drug)-[b]->(pat2:Pathology)
WHERE a.weight > 20 AND b.weight > 20
MERGE (pat1)<-[rel:PathoRelates]->(pat2)
ON CREATE SET rel.weight=1
ON MATCH SET rel.weight=r.weight+1
~~~

## Exercício 5

Construa um grafo ligando os medicamentos aos efeitos colaterais (com pesos associados) a partir dos registros das pessoas, ou seja, se uma pessoa usa um medicamento e ela teve um efeito colateral, o medicamento deve ser ligado ao efeito colateral.

### Resolução
~~~cypher
LOAD CSV WITH HEADERS FROM 'https://raw.githubusercontent.com/santanche/lab2learn/master/data/faers-2017/drug-use.csv' AS line
MATCH (droga:Drug {code: line.codedrug})
MERGE (pessoa:Person {idperson: line.idperson})
MERGE (pessoa)-[u:Usa]->(droga)
ON CREATE SET u.weight=1
ON MATCH SET u.weight=u.weight+1

LOAD CSV WITH HEADERS FROM 'https://raw.githubusercontent.com/santanche/lab2learn/master/data/faers-2017/sideeffect.csv' AS line
MATCH (patologia:Pathology {code: line.codePathology})
MERGE (pessoa:Person {id: line.idPerson})
MERGE (pessoa)-[e:Efeito]->(patologia)
ON CREATE SET e.weight=1
ON MATCH SET e.weight=e.weight+1

MATCH (droga:Drug)-[a]->(pessoa:Person)<-[b]-(patologia:Pathology)
WHERE a.weight > 20 AND b.weight > 20
MERGE (droga)-[g:Gera]->(patologia)
ON CREATE SET g.weight=1
ON MATCH SET g.weight=g.weight+1
~~~

## Exercício 6

Que tipo de análise interessante pode ser feita com esse grafo?

Proponha um tipo de análise e escreva uma sentença em Cypher que realize a análise.

### Resolução
~~~cypher
(escreva aqui a resolução em Cypher)
~~~
