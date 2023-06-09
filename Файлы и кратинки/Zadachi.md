# Описание задачи
- Справочник "Контрагенты"
При изменении наименования полное наименование должно заполняться с разворачиванием распространённых сокращений организационно-правовых форм. Например, АО «Вектор» должно превращаться в Акционерное общество «Вектор».
# Решение
&НаКлиенте

Процедура НаименованиеПриИзменении(Элемент)

	Сокращение = Новый Соответствие;
	Сокращение.Вставить("ГУП"," Государственное унитарное предприятие");
	Сокращение.Вставить("ИП" ," Индивидуальный предприниматель");
	Сокращение.Вставить("ОАО", " Открытое акционерное общество");
	Сокращение.Вставить("ЗАО", "Закрытое акционерное общество");
	Сокращение.Вставить("АО", "Акционерное общество");
	Сокращение.Вставить("ООО", "Общество ограниченной ответственностью");
	Сокращение.Вставить("ОДО", "Общество дополнительной ответственностью");
	Сокращение.Вставить("ФКП", "Федеральное казенное предприятие");
	Сокращение.Вставить("МУУП", "Муниципальное унитарное предприятие");
	Сокращение.Вставить("СХК", "Сельскохозяйственный кооператив");
	Сокращение.Вставить("ЖСК", "Жилищно строительный кооператив");
	Сокращение.Вставить("СМУ", "Строительно монтажное управление");
	Сокращение.Вставить("СОШ","Средняя обшеобразовательная школа");
	
	СловаНаименования =СтрРазделить( Объект.Наименование, " ");
	ПолноеНаименование = Новый Массив;
	
	Для Каждого Слово  Из СловаНаименования  Цикл
		Расшифровка = Сокращение.Получить(Слово);
		
		Если Расшифровка  =  Неопределено  Тогда 
			ПолноеНаименование.Добавить(Слово);
		Иначе
			ПолноеНаименование.Добавить(Расшифровка);
			
		КонецЕсли;
	КонецЦикла;
	Объект.ПолноеНаименование = СтрСоединить(ПолноеНаименование, " ");
КонецПроцедуры

- Справочник "Сотрудники"

 С модулем менеджера 

В котором переопределено получение формы объекта в зависимости от права доступа «Администрирование».
# Решение
&НаСервере

Процедура ОбработкаПолученияФормы(ВидФормы, Параметры, ВыбраннаяФорма, ДополнительнаяИнформация, СтандартнаяОбработка)
	
	Если ПравоДоступа("Администрирование", Метаданные) Тогда
		Форма = Метаданные.Справочники.Сотрудники.Формы.ФормаАдминистратора;
		Отказ = Истина;
	Иначе
		СтандартнаяОбработка = Истина; 
		Форма = Метаданные.Справочники.Сотрудники.Формы.ФормаПользователя; 
		Отказ=Ложь;
	КонецЕсли;
	
КонецПроцедуры


*С формой элемента «ФормаАдминистратора»:*

![Форма Администратора](%D0%A4%D0%BE%D1%80%D0%BC%D0%B0%20%D0%B0%D0%B4%D0%BC%D0%B8%D0%BD%D0%B8%D1%81%D1%82%D1%80%D0%B0%D1%82%D0%BE%D1%80%D0%B0.png)

- которая открывается для пользователей с правом доступа «Администрирование»;
- в которой, помимо реквизитов сотрудника, есть флажок «Вход разрешён» и поля ввода «Имя для входа» и «Пароль», отражающие свойства пользователя ИБ.
- # Решение
 &НаСервере

Процедура ПриЧтенииНаСервере(ТекущийОбъект) 
	
	Если  ЗначениеЗаполнено(ТекущийОбъект.ИдентификаторПользователяИБ)Тогда	
		ПользовательИБ = ПользователиИнформационнойБазы.НайтиПоУникальномуИдентификатору
		(ТекущийОбъект.ИдентификаторПользователяИБ);
		ИмяДляВхода = ПользовательИБ.Имя;
		Пароль = "";
		ВходРазрешен = ПользовательИБ.АутентификацияСтандартная;
	Иначе
		Если Не ЗначениеЗаполнено( ТекущийОбъект.ИдентификаторПользователяИБ) 
			Или ПользовательИБ =  Неопределено Тогда
			ВходРазрешен = Ложь;
			Пароль = "";
			ИмяДляВхода = "";			
		КонецЕсли;
	КонецЕсли;
	
КонецПроцедуры

&НаСервере

Процедура ПередЗаписьюНаСервере(Отказ, ТекущийОбъект, ПараметрыЗаписи) 
	
	Если  ЗначениеЗаполнено(ТекущийОбъект.ИдентификаторПользователяИБ) Тогда	
		ПользовательИБ = ПользователиИнформационнойБазы.НайтиПоУникальномуИдентификатору
		(ТекущийОбъект.ИдентификаторПользователяИБ); 
		ПользовательИБ.Имя = ИмяДляВхода;
		ПользовательИБ = Пароль;
		ПользовательИБ.АутентификацияСтандартная =ТекущийОбъект. ВходРазрешен;
	Иначе
		Если   Не ЗначениеЗаполнено( ТекущийОбъект.ИдентификаторПользователяИБ) И 
			ВходРазрешен = Истина Тогда 
			ПользовательИБ = ПользователиИнформационнойБазы.СоздатьПользователя();
			ПользовательИБ.АутентификацияСтандартная = Истина;
			ПользовательИБ.Пароль  =  Пароль;
			ПользовательИБ.Имя = ИмяДляВхода;
			ПользовательИБ.Роли.Добавить(Метаданные.Роли.БазовыеПрава);
			ПользовательИБ.Записать();
			ТекущийОбъект.ИдентификаторПользователяИБ = ПользовательИБ.УникальныйИдентификатор;
		КонецЕсли;
	КонецЕсли; 
	
КонецПроцедуры

&НаКлиенте

Процедура СлучайныйПароль(Команда) 
	
	СлучайныйПароль ="";
	СимволыПароля = "abcdefjhigklmnopqustv0123456789";
	ГСЧ = Новый ГенераторСлучайныхЧисел();
	Для СЧ =1  По 9  Цикл
		СлучайныйПароль = СлучайныйПароль + Сред ( СимволыПароля, 
		ГСЧ.СлучайноеЧисло( 1 , СтрДлина( СимволыПароля )),  1 );
	КонецЦикла;
	Пароль = СлучайныйПароль;
	
КонецПроцедуры
