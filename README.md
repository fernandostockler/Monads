# Result 
[![wakatime](https://wakatime.com/badge/github/fernandostockler/Result.svg?logo=github&style=plastic)](https://wakatime.com/badge/github/fernandostockler/Result)

## Descri��o
`Result` representa o resultado de uma opera��o. 
Se a opera��o falhar, `Result` conter� a exce��o ocorrida; 
caso contr�rio, `Result` conter� o valor resultante da opera��o.

O m�todo respons�vel pela opera��o deve retornar um `Result` com o valor resultante da opera��o, ou 
em caso de falha, deve retornar um `Result` com uma exce��o. O c�digo consumidor deve extrair o 
resultado atrav�s de um dos m�todos dispon�veis.

O resultado da opera��o pode ser extraido atrav�s de v�rios m�todos como: 
`Match()`, `CaseSuccess()`, `CaseFailure()`, `IsSuccess()` e `IsFailure()`.

Note que nenhuma exce��o � lan�ada. As exce��es s�o apenas criadas e devolvidas para o c�digo chamador que
dever� decidir o que fazer com elas.

## Uso
Exemplo de um m�todo respons�vel pela opera��o de valida��o.
```C#
public static Result<string> TryValidate(string? s, IFormatProvider? provider = default)
{
    if (s is null)
        return new Result<string>(new ArgumentNullException(nameof(s)));

    if (s.Length > MaxLength)
        return new Result<string>(new ArgumentException(
            message: $"{nameof(Name)} is invalid. The value '{s}' must be at most {MaxLength} characters and currently has {s.Length} characters.",
            paramName: nameof(s)));

    return new Result<string>(s);
}
```
<br>

Exemplo de dois m�todos que usam `Result`.

```C#
public static Result<Name> TryParse(string? s, IFormatProvider? provider = default) =>
    TryValidate(s, provider).Match(
        Success: x => new Result<Name>(new Name(x)),
        Failure: e => new Result<Name>(e));

public static bool IsValid(string? s, IFormatProvider? provider = null) =>
    TryValidate(s, provider).Match(
        Success: x => true,
        Failure: e => false);
```
<br>

## M�todos p�blicos
### Match
```C#
R Match<R>(Func<T, R> Success, Func<Exception, R> Failure)
```
    If it is a successful result, it returns the value of the Success function, 
    otherwise it returns the value of the Failure function.

```C#
Match(Action<T> Success, Action<Exception> Failure)
```
    If it is a successful result, it executes the Success action, otherwise it executes the Failure action.

### CaseSuccess
```C#
R CaseSuccess<R>(Func<T, R> Success, Func<Exception, R> defaultValue)
```
    If it is a successful result, it returns the value of the Success function, 
    otherwise it returns the value of the defaultValue function.
```C#
bool CaseSuccess(Action<T> Success)
```
    If it is a successful result, it executes the Success action, otherwise it returns false.
###  CaseFailure
```C#
T CaseFailure(Func<Exception, T> Failure) => Match(x => x, Failure)
```
    If it is a successful result, it returns the value of the result, otherwise it returns the value of the Failure function.
```C#
bool CaseFailure(Action<Exception> Failure)
```
    If it is a failure result, it executes the Failure action and return true, otherwise it returns false.
### IsSuccess
```C#
bool IsSuccess()
```
    If it is a successful result, it returns true, otherwise it returns false.
### IsFailure
```C#
bool IsFailure()
```
    If it is a failure result, it returns true, otherwise it returns false.

## References
C# functional language extensions  - [louthy/language-ext](https://github.com/louthy/language-ext)

Don't throw exceptions in C#. Do this instead - [Nick Chapsas](https://www.youtube.com/watch?v=a1ye9eGTB98&t=761s&ab_channel=NickChapsas)