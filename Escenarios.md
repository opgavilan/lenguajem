# Escenarios

## Indice

-[Remover columnas dinamicamente](#remover-columnas-dinamicamente)
## Fórmulas
### Remover columnas dinamicamente
<p align="center">
  <img src="https://github.com/user-attachments/assets/b52a54b2-13ac-4c68-bcf1-28b7b346caa2" width="700" height="300">
</p>

```
LimpiezaColumnas = Table.RemoveColumns(  
    TablaOrigen,  
    List.Select(  
    Table.ColumnNames(TablaOrigen),  
    each not (Text.Contains(_,"Observa") or Text.Contains(_,"FECHA"))  
    )  
)
```
