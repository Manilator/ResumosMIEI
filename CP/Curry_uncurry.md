# Revisões

Vamos apenas rever aplicação parcial de funções, o que é uma função curried e uncurried.

# Currying

`Currying` ou `aplicação parcial de uma função` é o acto de se aplicar uma função sem passar todos os argumentos que ela pede.
Vamos ver um exemplo.

Por simplicidade vamos definir uma função básica.
```haskell
mySumInt :: Int -> Int -> Int
mySumInt x y = x + y
```
A partir do tipo da função, para alguem iniciado em Haskell, não é muito intuitivo que esta função recebe dois `Ints` e devolve outro.

Isto acontece porque na realidade as funções em Haskell recebem apenas um argumento.
Funções com vários argumentos, vão aplicando o primeiro argumento (o mais a esquerda) e devolvem uma nova função que pede menos um argumento.
```haskell
> :type mySumInt
mySumInt :: Int -> Int -> Int

> :type mySumInt 3
mySumInt 3 :: Int -> Int

> let mySumInt3 = mySumInt 3
> :type mySumInt3
mySumInt3 :: Int -> Int
```
Como podemos ver, ao aplicar a `mySumInt` a apenas um `Int`, temos uma nova função, que aceita um `Int` e devolve o resultado da soma desse `Int` com `3`.
Ou seja, o primeiro argumento da `mySumInt` foi substituido por `3` e temos uma função nova que soma um `Int` qualquer a `3`.

De facto, podemos pensar em `aplicação parcial` de uma função como uma especialização. A função `mySumInt` somava dois `Ints` e apos aplicada parcialmente soma um `Int` a `3`, que é um caso muito mais especifico.

```haskell
mySumInt 3 4

(mySumInt 3) 4

3 + 4
```
Ou por tipos
```haskell
> :type mySumInt
mySumInt :: Int -> Int -> Int

> :type mySumInt 3
mySumInt 3 :: Int -> Int

> :type mySumInt 3 4
mySumInt 3 4 :: Int
```

**Porque é que isto importa?**

Se nos abstrairmos do modelo de pensamento imposto por outros paradigmas de programação, podemos começar a pensar em funções como bem mais que blocos de código cujo **único** propósito é o retorno de dados processados.
Podemos então começar a pensar em funções como geradores de **novas** funções, dado esta nova relação entre elas.

## Curry and uncurry

Como ja vimos, todas as funções em `Haskell` recebem apenas um argumento.
Funções com vários argumentos vão aplicando parcialmente a função aos seus argumentos até que se tornem numa função com um só argumento.

As funções em `Haskell` possuem então duas formas `curried` e `uncurried`.

As funções `curried` são as que costumamos trabalhar normalmente
```haskell
mySumInt :: Int -> Int -> Int
mySumInt x y = x + y
```
Visto que possuem mais que um argumento, podem ser aplicadas parcialmente. O seu resultado nao tem que ser totalmente calculado logo. Pode receber um argumento, espera por um segundo argumento e vai 'calculando' o que pode com este primeiro argumento, recebe o segundo, calcula o que pode, espera por outro argumento e quando tiver todos os argumentos, calcula o que falta. Desta forma o tempo de calculo (processamento da função) é distribuido, nao tendo que ficar a espera de todos os argumentos.


A segunda forma é a `uncurried`
```haskell
mySumInt :: (Int, Int) -> Int
mysumInt (x, y) = x + y
```
Esta forma recebe apenas **um** argumento sendo que para executar, tem que ter os dados (elementos do tuplo) presentes ao mesmo tempo.


Existem funções para passar de um estado para o outro.
Curiosamente só podem ser definidas desta forma.
```haskell
curry :: ((a, b) -> c) -> (a -> b -> c)
curry f a b = f (a,b)

uncurry :: (a -> b -> c) -> (a, b) -> c
uncurry f (a, b) = f a b
```
Os nomes podem parecer trocados mas analisando os tipos fazem perfeito sentido. Vamos fazer esse exercicio por alto.

```haskell
curry :: ((a, b) -> c) -> (a -> b -> c)
curry f a b = f (a,b)
```
  * `f` é uma função que recebe um tuplo
  * `a` é um tipo qualquer que é passado separadamente do `b`
  * `b` é um tipo qualquer que é passado separadamente do `a`
O que a `curry` faz é agrupar o `a` e `b` num tuplo `(a,b)` e chamar a função `f`, que recebe um tuplo.


```haskell
uncurry :: (a -> b -> c) -> (a, b) -> c
uncurry f (a, b) = f a b
```
  * `f` é uma função que recebe dois argumentos separados
  * `a` e `b` são de tipos diferentes, qualquer, passados juntos como um tuplo
O que a `uncurry` faz é separar o `a` e `b`,e chamar a função `f`, que recebe os argumentos em separado.