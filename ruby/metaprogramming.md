# Metaprogramming Ruby - Paolo Perrota

## Monday

Emitir recibo 

Fundamentos 
  Dado uma compra aprovada pelo sistema executor
  Quando retornado uma resposta de aprovação
  Então é gerado um recibo no sistema

  Cenario: uma cobrança tem status paid

    Dado que a cobrança é criada
    Quando seu status mudar para paid
    Então um recibo é criado

  Cenario: um recibo é gerado
    
    Dado um recibo gerado 
    Quando acessado o recibo 
    Então deve conter Data de vencimento da cobrança.
    Então deve conter Data em que o pagamento foi efetivado.
    Então deve conter Código de autorização do banco.
Thuesday

