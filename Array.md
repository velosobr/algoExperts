# VELOCODE · ALGORITHMS & DATA STRUCTURES

## Capítulo 2 — ARRAYS

**Static · Dynamic · Complexidade · 80/20 FAANG**

> A estrutura de dados que aparece em 70% das entrevistas — teoria profunda, nenhum enrolamento.

---

## 📌 O que é um Array de Verdade

### Teoria da memória (nível entrevista)

Arrays são um **bloco contíguo de memória** onde:

* Cada elemento ocupa o mesmo número de bytes
* O endereço de qualquer elemento é **calculável em tempo constante**

👉 Definição formal:

```text
Array = memória contígua + tamanho fixo de elemento + acesso direto via índice
```

---

## ⚡ A Matemática do Acesso O(1)

```text
address(arr[i]) = base_address + (i × element_size)
```

### Exemplo

```text
base = 0x1000
element_size = 4 bytes

arr[5] = 0x1000 + (5 × 4) = 0x1014
```

### 🔑 Insight

* Não importa o tamanho do array
* A CPU faz **1 cálculo + 1 leitura**
* Portanto → **O(1)**

### 🧠 Analogia

Um estacionamento numerado:

* Você não percorre vaga por vaga
* Você calcula direto: `entrada + índice × tamanho`

---

## 🧠 Por que Contiguidade Importa (Cache Locality)

* CPUs leem **blocos de memória (cache line ~64 bytes)**
* Acessar `arr[0]` já carrega vários elementos no cache

### Consequência

* Iteração em array → **muito rápida na prática**
* LinkedList → **cache miss constante**

👉 Ambos são O(n), mas:

> Arrays são **ordens de magnitude mais rápidos** na prática

---

## 🎯 Static vs Dynamic Arrays

---

### 🔹 Static Array

* Tamanho fixo
* Sem realocação
* Zero overhead

```kotlin
val arr = IntArray(5)
arr[0] = 42
// arr[5] → Exception
```

### Trade-off

| Vantagem           | Desvantagem            |
| ------------------ | ---------------------- |
| Performance máxima | Não cresce             |
| Sem overhead       | Precisa prever tamanho |

---

### 🔹 Dynamic Array (ArrayList / MutableList)

* Cresce automaticamente
* Internamente = array estático + resize

---

## 🔄 Como o Resize Funciona

1. Cria novo array (geralmente 2× maior)
2. Copia elementos → O(n)
3. Descarta antigo
4. Adiciona novo item

---

## 📊 Amortização (O(1) amortizado)

```text
1 + 2 + 4 + ... + n = 2n - 1
```

👉 Custo médio:

```text
(2n) / n ≈ 2 → O(1)
```

### ⚠️ Entrevista

* ❌ Errado: `O(1)`
* ✅ Correto:
  **O(1) amortizado, O(n) no pior caso**

---

## ⚖️ Static vs Dynamic — Quando Usar

| Critério            | Static  | Dynamic        |
| ------------------- | ------- | -------------- |
| Tamanho conhecido   | ✅ Ideal | ⚠️ desperdício |
| Tamanho variável    | ❌       | ✅              |
| Performance crítica | ✅       | ⚠️ resize      |
| Memória limitada    | ✅       | ❌ até 2×       |
| Acesso aleatório    | ✅ O(1)  | ✅ O(1)         |
| Insert no meio      | ❌       | ❌ O(n)         |

---

## 📊 Big O de Arrays (com explicação)

### 🔍 Leitura

| Operação           | Tempo    | Porquê            |
| ------------------ | -------- | ----------------- |
| `arr[i]`           | O(1)     | cálculo direto    |
| Search (unsorted)  | O(n)     | precisa percorrer |
| Binary Search      | O(log n) | divide por 2      |
| Min/Max (unsorted) | O(n)     | precisa ver todos |

---

### ➕ Inserção

| Operação         | Tempo | Porquê        |
| ---------------- | ----- | ------------- |
| End (sem resize) | O(1)  | posição livre |
| End (resize)     | O(n)  | cópia         |
| Beginning        | O(n)  | shift         |
| Index i          | O(n)  | shift         |

---

### ➖ Remoção

| Operação  | Tempo | Porquê     |
| --------- | ----- | ---------- |
| End       | O(1)  | decremento |
| Beginning | O(n)  | shift      |
| Index i   | O(n)  | shift      |

---

### 🔄 Derivadas

| Operação  | Tempo      |
| --------- | ---------- |
| Traversal | O(n)       |
| Reverse   | O(n)       |
| Sort      | O(n log n) |
| Copy      | O(n)       |

---

## 🔑 Regra Mental Universal

```text
Quebrou contiguidade → O(n)
Usou índice direto → O(1)
Sem saber onde está → O(n)
Pode dividir ao meio → O(log n)
```

---

## ❗ Por que Insert/Delete no Meio é O(n)

### Exemplo

```text
[ A ][ B ][ C ][ D ][ E ]

Inserir X em posição 2:

→ shift E
→ shift D
→ shift C

Resultado:
[ A ][ B ][ X ][ C ][ D ][ E ]
```

👉 Pior caso = **n shifts**

---

### ⚠️ Armadilha clássica

```kotlin
for (...) {
    list.remove(...)
}
```

👉 Resultado:

```text
O(n) loop × O(n) shift = O(n²)
```

---

## 🚀 Os 5 Padrões 80/20 (FAANG)

---

## 1️⃣ Two Pointers

### Quando usar

* Array ordenado
* Par com soma alvo
* Remoção in-place

### Exemplo

```kotlin
fun twoSumSorted(numbers: IntArray, target: Int): IntArray {
    var left = 0
    var right = numbers.lastIndex

    while (left < right) {
        val sum = numbers[left] + numbers[right]
        when {
            sum == target -> return intArrayOf(left + 1, right + 1)
            sum < target  -> left++
            else          -> right--
        }
    }
    return intArrayOf()
}
```

---

## 2️⃣ Sliding Window

### Quando usar

* Subarrays contíguos
* Soma, média, tamanho máximo

```kotlin
fun findMaxAverage(nums: IntArray, k: Int): Double {
    var windowSum = nums.take(k).sum()
    var maxSum = windowSum

    for (i in k until nums.size) {
        windowSum += nums[i] - nums[i - k]
        maxSum = maxOf(maxSum, windowSum)
    }
    return maxSum.toDouble() / k
}
```

---

## 3️⃣ Prefix Sum

### Ideia

```text
prefix[i] = soma de arr[0..i-1]
```

```kotlin
fun buildPrefix(arr: IntArray): IntArray {
    val prefix = IntArray(arr.size + 1)
    for (i in arr.indices) {
        prefix[i + 1] = prefix[i] + arr[i]
    }
    return prefix
}

fun rangeSum(prefix: IntArray, i: Int, j: Int): Int {
    return prefix[j + 1] - prefix[i]
}
```

---

## 4️⃣ Kadane’s Algorithm

```kotlin
fun maxSubArray(nums: IntArray): Int {
    var currentSum = nums[0]
    var globalMax  = nums[0]

    for (i in 1 until nums.size) {
        currentSum = maxOf(nums[i], currentSum + nums[i])
        globalMax  = maxOf(globalMax, currentSum)
    }
    return globalMax
}
```

👉 **Complexidade: O(n) / O(1)**

---

## 5️⃣ Index as Hash / Cyclic Sort

### Quando usar

* Valores no range `[1..n]` ou `[0..n-1]`

```kotlin
fun findDuplicates(nums: IntArray): List<Int> {
    val result = mutableListOf<Int>()

    for (num in nums) {
        val idx = Math.abs(num) - 1
        if (nums[idx] < 0) {
            result.add(Math.abs(num))
        } else {
            nums[idx] = -nums[idx]
        }
    }
    return result
}
```

---

## 🧭 Guia Rápido de Reconhecimento

| Problema           | Padrão         |
| ------------------ | -------------- |
| Par com soma alvo  | Two Pointers   |
| Subarray tamanho k | Sliding Window |
| Subarray variável  | Sliding Window |
| Soma de subarray   | Prefix Sum     |
| Máximo subarray    | Kadane         |
| Duplicatas [1..n]  | Index as Hash  |
| In-place           | Two Pointers   |
| Array sorted       | Binary Search  |

---

## 🧠 Resumo Final

* Arrays = **contiguidade + acesso direto**
* Performance real = **cache locality**
* Dynamic arrays = **amortização**
* Insert/Delete no meio = **O(n) sempre**
* 5 padrões resolvem **~90% dos problemas**

---

Se quiser, posso transformar isso no **Capítulo 3 (Linked Lists)** no mesmo nível — mantendo consistência de documentação estilo FAANG-ready.
