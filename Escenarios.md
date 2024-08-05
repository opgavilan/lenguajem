# Escenarios

## Indice

-[Remover columnas dinamicamente](#remover-columnas-dinamicamente)  
-[Reemplazar varios datos de una columna](#reemplazar-varios-datos-de-una-columna)
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

### Reemplazar varios datos de una columna

<p align="center">
  <img src="https://github.com/user-attachments/assets/54e36002-1984-4fab-9ed4-e0375d581dbd" width="700" height="300">
</p>

```

   FuncionReemplazoColumna = 
   let
    // Datos de ejemplo
    TablaFijada = #"Columnas con nombre cambiado",
    
    // Lista de reemplazos: {ValorAntiguo, ValorNuevo}
    Replacements = {
        {"SI", "1"},
        {"NO PASA", "0"},
        {"NO","0"},
        {"PASA","1"}
    },
    
    // Función que aplica los reemplazos
    ReplaceFunction = (table as table, Replacements as list) as table =>
        List.Accumulate(
            Replacements,
            TablaFijada,
            (state, current) => Table.ReplaceValue(
                state,
                current{0},
                current{1},
                Replacer.ReplaceText,
                {"saludo"}
            )
        ),

    // Aplicar la función de reemplazo a la tabla original
    Result = ReplaceFunction(TablaFijada, Replacements)
    in
    Result,
```
