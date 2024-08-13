# Escenarios

## Indice

-[Remover columnas dinamicamente](#remover-columnas-dinamicamente)  
-[Reemplazar varios datos de una columna](#reemplazar-varios-datos-de-una-columna)  
-[Reemplazar nombre de columnas](#reemplazar-nombre-de-columnas)  
-[Tabla Calendario](#tabla-calendario)  
-[Combinar Consultas con M](#combinar-consultas-con-m)  

## Fórmulas
### Remover columnas dinamicamente
<p align="center">
  <img src="https://github.com/user-attachments/assets/b52a54b2-13ac-4c68-bcf1-28b7b346caa2" width="700" height="300">
</p>

``` sql
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

``` sql

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
                {"saludo"} // Se puede colocar diversas columnas
            )
        ),

    // Aplicar la función de reemplazo a la tabla original
    Result = ReplaceFunction(TablaFijada, Replacements)
    in
    Result,
```
### Reemplazar nombre de columnas

<p align="center">
  <img src="https://github.com/user-attachments/assets/88012734-04b9-4165-83ea-33829c6c7162" width="700" height="300">
</p>

``` sql
let
    Origen = Excel.CurrentWorkbook(){[Name="Tabla1"]}[Content],
    ColumnasOrigen = Table.FromList(Table.ColumnNames(Origen))[Column1],
    ColumnasCambio = 
    Table.ReplaceValue(
        Table.FromList(
            Table.ColumnNames(Origen)
        ),
        "detale (pasa)",
        "detalle",
        Replacer.ReplaceText,
        {"Column1"}
    )[Column1],
    ListaDeCambios = List.Zip({ColumnasOrigen,ColumnasCambio}),
    Resultado = Table.RenameColumns(Origen,ListaDeCambios)
in
    Resultado    
```

### Tabla Calendario

``` sql
let
    fechaInicial = Date.FromText("01/01/2024"),
    Calendario = Table.FromList(
        List.Dates(fechaInicial,366,#duration(1,0,0,0)),Splitter.SplitByNothing(),null,null, ExtraValues.Error 
    ),
    #"Tipo cambiado" = Table.TransformColumnTypes(Calendario,{{"Column1", type date}}),
    #"Columnas con nombre cambiado" = Table.RenameColumns(#"Tipo cambiado",{{"Column1", "Fecha"}}),
    #"Mes insertado" = Table.AddColumn(#"Columnas con nombre cambiado", "Mes", each Date.Month([Fecha]), Int64.Type),
    #"Nombre del mes insertado" = Table.AddColumn(#"Mes insertado", "Nombre del mes", each Date.MonthName([Fecha]), type text),
    #"Año insertado" = Table.AddColumn(#"Nombre del mes insertado", "Año", each Date.Year([Fecha]), Int64.Type),
    #"Día insertado" = Table.AddColumn(#"Año insertado", "Día", each Date.Day([Fecha]), Int64.Type),
    PeriodoAgregado = Table.AddColumn(#"Día insertado", "PeriodoMes", each if
        Text.Length(Text.From([Mes]))= 1
      then
        Text.From([Año]) &"-0"& Text.From([Mes])
      else 
        Text.From([Año]) &"-"& Text.From([Mes]))
in
    PeriodoAgregado

```

### Combinar Consultas con M

```sql
= Table.ExpandTableColumn(
    Table.NestedJoin(#"Filas filtradas","AGENTE",dimAgente,"DNI_AGENTE","Nombre Agente",JoinKind.LeftOuter),
    "Nombre Agente",
    {"NOMBRE_AGENTE"}, 
    {"NOMBRE_AGENTE"}
  )

```
