# Калькулятор
Модуль располагается в репозитории `@creditclub/helpers@^7.4`

## Параметры
```
constructor({
    type?: 'ANNUITY' | 'PERCENT';
    loan?: string | number;
    period?: string | number;
    payment?: string | number;
    percent?: string | number;
});

// Возращаемое при вызове метода build()
CalculationResults {
    ROE: number;
    payment: number;
    overPayment: number;
    loan: number;
    period: number;
    percent: number;
    type: 'ANNUITY' | 'PERCENT';
}
```

## Методы
`calculate: Calculation` процедура расчёта калькулятора и синхронизация результирующего значения
с временным

`set: Calculation` установить новое значение в указанное свойство

`get: number | string` получить значение по названию свойства

`isCalculation` проверка на принадлежность к данному классу

`build` получить обьект калькулятора

## Описание взаимодействия
Данный модуль используется как в обычном формировании расчёта так и в реактивном.
Каждый раз как только в калькулятор заходит новое значение, например через метод `set`, он возвращает новый
экземпляр класса `Calculation` с сохранёнными параметрами от предыдущего и изменённым значением.

Калькулятор хранит несколько вариантов расчётов, временный и результирующий. метод `get` возвращает
поля из временного.

Когда происходит `set` значения результат расчёта калькулятора сохранается в результирующем обьекте,
однако `get` будет получать ещё старое значение на основании ввода в `set`, для их синхронизации
необходимо вызвать метод `calculate` и сохранить результат в состоянии. Это необходимо что бы данные от
ввода всегда соответствовали и изменялись лишь при пересчёте. См. пример с React.

## Примеры использования
Если вам необходио создать расчёт на основании ввода пользователя и например отправить его на сервер, или что то ещё, то можно
сразу передать данные в конструктор и сбилдить расчёт
```
const calc = new Calculation({
    loan: 200000,
    period: 12,
});

api.send(calc.build());
```

Если необходима форма где значения калькулятора синхронизируются:
```
const ReactForm = () => {
    const [calc, setCalc] = useState(new Calculation());

    const handleChange = (value) => {
        setCalc(calc.set('loan', value));
    }

    const handleBlur = () => {
        setCalc(calc.calculate());
    }

    handleSubmit = () => {
        onSubmit(calc.build())
    }

    return (
        <input
            value={calc.get('loan')}
            onChange={handleChange}
            onBlur={handleBlur}
        />
        <input type="submit" onSubmit={handleSubmit} />
    )
}
```
