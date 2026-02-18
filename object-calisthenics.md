## Object Calisthenics

>  O termo object calisthenics foi apresentado pela primeira vez por Jeff Bay. E se trata de um conjunto de 9 regras/boas práticas de codificação, que visam maior legibilidade e manutenibilidade de um sistema.

* **1 - Usar apenas um nível de identação por método (Use only one level of indentation per method.)**
Quanto mais níveis de identação adicionamos, mais a lógica dentro de cada um deles vai ficando complexa de acompanhar, com isso fica mais fácil erros acontecerem, abaixo segue um exemplo simples, mas sabemos que no dia a dia isso pode ficar cada vez maior.

```
//Wrong way

ClassMethod UpdateProductData(ProductData As %DynamicObject) As %Status
{
    Set st = $$$OK
    Try
    {
        //Level 0
        Set Product = ##class(CalisthenicSystem.Data.Product).%OpenId(ProductData.id,,.st)
        If '$System.Status.IsError(st)
        {
            //Level 1
            If (ProductData.price '= "")
            {
                //Level 2
                If (ProductData.price > 0)
                {
                    Set Product.Price = ProductData.price
                }
                else 
                {
                    Set Exception = ##class(%Exception.General).%New("The product price should be greater than zero...", 999)
                    Throw Exception
                }
            }

            Set st = Product.%Save()
            If $System.Status.IsError(st) Throw ##class(%Exception.StatusException).CreateFromStatus(st)
        }
        else
        {
            Set Exception = ##class(%Exception.General).%New("Error when trying to update a product...", 999)
            Throw Exception
        }

    }
    Catch(Error)
    {
        Set st = Error.AsStatus()
    }
    Return st
}

```

```
//Correct way

ClassMethod UpdateProductData(ProductData As %DynamicObject) As %Status
{
    Set st = $$$OK
    Try
    {
        //Level 0
        Set Product = ##class(CalisthenicSystem.Data.Product).%OpenId(ProductData.id,,.st)
        If '$System.Status.IsError(st)
        {
            //Level 1
            Set st = ..UpdateProductPrice(Product, ProductData.price)
            If $System.Status.IsError(st) Throw ##class(%Exception.StatusException).CreateFromStatus(st)
        
            Set st = Product.%Save()
            If $System.Status.IsError(st) Throw ##class(%Exception.StatusException).CreateFromStatus(st)
        }
        else
        {
            Set Exception = ##class(%Exception.General).%New("Error when trying to update a product...", 999)
            Throw Exception
        }

    }
    Catch(Error)
    {
        Set st = Error.AsStatus()
    }
    Return st
}

ClassMethod UpdateProductPrice(Product As CalisthenicSystem.Data.Product, NewPrice As %Integer) As %Status
{
    Set st = $$$OK
    Try
    {
        //Level 0
        Quit:(NewPrice = "")

        If (NewPrice > 0)
        {
            //Level 1
            If (NewPrice > 0)
            {
                Set Product.Price = NewPrice
            }
            else 
            {
                Set Exception = ##class(%Exception.General).%New("The product price should be greater than zero...", 999)
                Throw Exception
            }
        }
    }
    Catch(Error)
    {
        Set st = Error.AsStatus()
    }
    Return st
}

```
