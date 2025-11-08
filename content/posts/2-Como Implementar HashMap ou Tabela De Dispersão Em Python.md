---
title: 'Como Implementar Sua Própria Tabela de Dispersão (HashMap) Utilizando Python'
tags: ["python", "hashmap", "estrutura de dados"]
draft: false
date: 2025-11-08 00:00:00.000 -0300
---

## Como Implementar Sua Própria Tabela de Dispersão (HashMap) Utilizando Python

De início, pode parecer algo complicado, mas assim que entendemos, deixa de ser. Basicamente, uma tabela de dispersão é uma estrutura de chave-valor. Ela é composta por um array (ou lista) de tamanho pré-definido e, em cada posição desse array, existe outra estrutura (como uma lista) que armazena os pares de chave e valor.

### Entendendo a parte técnica

Para otimizar o tempo de busca, ter dados indexados é fundamental. Em estruturas como listas ou arrays simples, para encontrar um item, você pode ter que percorrer toda a coleção, resultando em uma complexidade de tempo de O(N) (linear). O HashMap resolve isso de forma brilhante. A ideia central é:

Função de Hash: Criar uma função que transforma qualquer chave (seja uma string, número, etc.) em um número inteiro. Esse número é o "hash".

Dispersão (Indexação): Usar esse hash para calcular um índice no array principal. A forma mais simples é usar o operador módulo (%) com o tamanho do array: index = hash(key) % array_size.

Armazenamento: O par (chave, valor) é armazenado na "sublista" (ou "balde") localizada nesse índice.

Quando você quer buscar um dado, o processo é o mesmo: você recalcula o hash da chave, encontra o índice e vai direto para o balde correto.

### O Problema da Colisão

O cenário ideal (sem colisões) acontece quando cada chave gera um índice único. No entanto, é inevitável que chaves diferentes gerem o mesmo índice. Isso é chamado de colisão. É aqui que a estratégia de "baldes" (buckets) entra:

Encadeamento Separado (Separate Chaining): Em vez de armazenar um único valor no índice, armazenamos uma lista (o balde). Se uma colisão ocorre, simplesmente adicionamos o novo par (chave, valor) a essa lista.

### Complexidade

Caso Médio/Ideal O(1): Se a função de hash for boa e o array tiver um tamanho adequado, os baldes terão poucos itens. O tempo para encontrar o item é considerado constante.

Pior Caso O(N): Se tudo colidir no mesmo balde (uma função de hash ruim ou azar extremo), o HashMap degenera em uma única lista encadeada, e a busca volta a ser O(N).

### Contexto: Implementação Didática vs. Prática Real

#### É crucial entender o contexto desta discussão:

Na prática, você raramente (ou nunca) precisará criar sua própria Tabela de Dispersão. Estruturas como HashMap em Java, Dictionary em C# ou o próprio dict em Python já são fornecidas nativamente pela linguagem. Elas são implementações robustas, exaustivamente testadas e altamente otimizadas.

O exercício de criar uma manualmente, como neste post, tem um propósito puramente didático: serve apenas para entender o funcionamento interno e os conceitos de hash, baldes e tratamento de colisões.

### Implementação

#### Construtor

Vamos começar criando a classe. O construtor (__init__) define o tamanho (número de baldes) e inicializa a estrutura principal com listas vazias (os baldes).
```python
class HashMap:
    def __init__(self, size=3):
        """
        Inicializa a tabela.
        'size' é o número de "baldes" (buckets).
        """
        self.size = size
        # Cria a lista de baldes (listas vazias)
        self.buckets = [[] for _ in range(self.size)]
```

### Cálculo Hash e Dispersão

Agora, vamos implementar o cálculo de hash. Este método interno (_hash) será usado por todas as outras operações. Basicamente, obtemos o resto da divisão do hash nativo da chave pelo tamanho da nossa tabela. Esse resto será o índice, que estará sempre entre 0 e self.size - 1.

(Para um size=3, a estrutura seria [ [], [], [] ])

```python
def _hash(self, key) -> int:
    """
    Calcula o índice (hash) para uma chave.
    """
    # 1. hash() obtém o hash da chave
    # 2. % self.size garante que o índice caiba na tabela
    return hash(key) % self.size
```

### Inserção e Atualização (put)

Em um HashMap, não temos um insert separado. Usamos um método (aqui chamado de put) que faz "upsert":

Verifica se a chave já existe.

Se existir, atualiza o valor.

Se não existir, insere o novo par (chave, valor).
```python
def put(self, key, value) -> bool:
    """
    Insere ou atualiza um par chave-valor (comportamento "upsert").
    """
    try:
        index = self._hash(key)
        bucket = self.buckets[index]

        # Verifica se a chave já existe para atualização
        for i, (k, v) in enumerate(bucket):
            if k == key:
                bucket[i] = (key, value) # Atualiza o valor
                return True

        # Se a chave não foi encontrada, insere como nova
        bucket.append((key, value))
        return True
    except Exception as e:
        # Este try/except é vago, mas mantido da sua lógica
        print(f"Não foi possível adicionar os dados: {e}")
        return False
```

### Busca (get)

A classe seria inútil sem uma forma de buscar os dados. O método get encontra o balde e o percorre. Se achar a chave, retorna o valor. Se não achar, levanta um KeyError, que é o comportamento padrão do dict do Python.
```python
def get(self, key):
    """
    Busca um valor pela chave.
    """
    index = self._hash(key)
    bucket = self.buckets[index]

    # Percorre o balde procurando pela chave
    for k, v in bucket:
        if k == key:
            return v
    
    # Se não encontrou, levanta um erro
    raise KeyError(f"Chave '{key}' não encontrada.")
```

### Remoção (remove)

Para remover, encontramos o balde correto e o percorremos. Se a chave for encontrada, removemos o par (chave, valor) da lista.
```python
def remove(self, key) -> bool:
    """
    Remove um par chave-valor pela chave.
    """
    index = self._hash(key)
    bucket = self.buckets[index]

    # Percorre o balde
    for i, (k, v) in enumerate(bucket):
        # Verifica a chave exata
        if k == key:
            bucket.pop(i) # Remove o item pelo índice 'i'
            return True
    
    # Retorna False APÓS o loop, se não encontrou
    return False
```

### Testando

#### Vamos verificar se a nossa classe funciona.

```python
# Criando nossa tabela com 5 baldes
my_map = HashMap(size=5)

# Inserindo dados
my_map.put("banana", 1)
my_map.put("maca", 2)
my_map.put("uva", 3)
my_map.put("laranja", 4) # Provavelmente causará uma colisão

# Visualizando a tabela interna (para fins de aprendizado)
# Você pode ver as colisões nos baldes
print("\n--- Estrutura Interna da Tabela ---")
for i, bucket in enumerate(my_map.buckets):
    print(f"Balde {i}: {bucket}")
print("----------------------------------\n")

# Buscando dados (acesso rápido)
try:
    print(f"Valor de 'maca': {my_map.get('maca')}")
    print(f"Valor de 'uva': {my_map.get('uva')}")
    
    # Atualizando um valor
    print("Atualizando 'maca' para 99...")
    my_map.put("maca", 99)
    print(f"Novo valor de 'maca': {my_map.get('maca')}")

    # Removendo um valor
    print("Removendo 'uva'...")
    my_map.remove("uva")
    print(f"Valor de 'uva': {my_map.get('uva')}") # Isso vai falhar

except KeyError as e:
    print(e)

# Testando busca por algo que não existe
try:
    print(f"Valor de 'abacate': {my_map.get('abacate')}")
except KeyError as e:
    print(e)
```

### Referências

- RealPython: https://realpython.com/python-hash-table/

- Augusto Gallego: https://www.youtube.com/watch?v=J4ELMYEGVS0