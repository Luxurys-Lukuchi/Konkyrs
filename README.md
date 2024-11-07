# Konkyrs
## Презентация

[Посмотреть презентацию](https://github.com/Luxurys-Lukuchi/Konkyrs/raw/main/Analis.pdf)


program CacheSizeDetermination;

{$APPTYPE CONSOLE}

uses
  SysUtils, Windows;

const
  ArraySize = 1024 * 1024; // Размер массива (1 миллион элементов)
  ElementSize = SizeOf(Integer); // Размер одного элемента массива

var
  arr: array[0..ArraySize - 1] of Integer;
  i, CacheBoundaryIndex: Integer;
  startTime, endTime: Int64;
  frequency: Int64;
  elapsedTime: Double;
  thresholdTime: Double;
  currentTime: Double;

begin
  // Инициализация массива единичными элементами
  for i := 0 to ArraySize - 1 do
    arr[i] := 1;

  // Получение частоты таймера
  QueryPerformanceFrequency(frequency);
  thresholdTime := 0;
  CacheBoundaryIndex := -1;

  // Основной цикл для определения индекса кэш-границы
  for i := 0 to ArraySize - 1 do
  begin
    // Запуск таймера
    QueryPerformanceCounter(startTime);

    asm
      // Загружаем элемент массива в регистр, выполняем операцию и сохраняем обратно
      mov eax, [arr + i * ElementSize]  // загружаем элемент массива
      add eax, 1                         // простая арифметическая операция
      mov [arr + i * ElementSize], eax   // сохраняем обратно
    end;

    // Остановка таймера
    QueryPerformanceCounter(endTime);

    // Вычисление времени выполнения
    elapsedTime := (endTime - startTime) * 1000000 / frequency;

    // Если текущее время больше установленного порога, сохраняем индекс
    if (elapsedTime > thresholdTime * 1.5) and (CacheBoundaryIndex = -1) then
    begin
      CacheBoundaryIndex := i;
    end;

    // Обновляем среднее время выполнения на основании текущего значения
    thresholdTime := (thresholdTime * i + elapsedTime) / (i + 1);
  end;

  // Вывод результатов
  if CacheBoundaryIndex <> -1 then
  begin
    WriteLn('Кэш-память закончилась на индексе: ', CacheBoundaryIndex);
    WriteLn('Примерный объем кэш-памяти: ', CacheBoundaryIndex * ElementSize div 1024, ' KB');
  end
  else
    WriteLn('Не удалось определить объем кэш-памяти');

  ReadLn;
end.






{$APPTYPE CONSOLE}

uses
  SysUtils, Windows;

const
  ArraySize = 1024 * 1024; // Размер массива (1 миллион элементов)
  ElementSize = SizeOf(Integer); // Размер одного элемента массива
  ConstantValue = 1; // Константа для арифметической операции

var
  arr: array[0..ArraySize - 1] of Integer;
  i, CacheBoundaryIndex: Integer;
  startTime, endTime: Int64;
  frequency: Int64;
  elapsedTime: Double;
  thresholdTime: Double;

begin
  // Инициализация массива единичными элементами
  for i := 0 to ArraySize - 1 do
    arr[i] := 1;

  // Получение частоты таймера
  QueryPerformanceFrequency(frequency);
  thresholdTime := 0;
  CacheBoundaryIndex := -1;

  // Основной цикл для определения индекса кэш-границы
  for i := 0 to ArraySize - 1 do
  begin
    // Запуск таймера
    QueryPerformanceCounter(startTime);

    asm
      mov eax, ConstantValue             // загружаем константу в eax
      add [arr + i * ElementSize], eax   // добавляем константу к элементу массива
    end;

    // Остановка таймера
    QueryPerformanceCounter(endTime);

    // Вычисление времени выполнения
    elapsedTime := (endTime - startTime) * 1000000 / frequency;

    // Если текущее время больше установленного порога, сохраняем индекс
    if (elapsedTime > thresholdTime * 1.5) and (CacheBoundaryIndex = -1) then
    begin
      CacheBoundaryIndex := i;
    end;

    // Обновляем среднее время выполнения на основании текущего значения
    thresholdTime := (thresholdTime * i + elapsedTime) / (i + 1);
  end;

  // Вывод результатов
  if CacheBoundaryIndex <> -1 then
  begin
    WriteLn('Кэш-память закончилась на индексе: ', CacheBoundaryIndex);
    WriteLn('Примерный объем кэш-памяти: ', CacheBoundaryIndex * ElementSize div 1024, ' KB');
  end
  else
    WriteLn('Не удалось определить объем кэш-памяти');

  ReadLn;
end.
