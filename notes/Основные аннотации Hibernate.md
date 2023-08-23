---
tags: ORM
--- 
@Entity javax.persistence.Entity  **обязательная аннотация**
@Table javax.persistence.Table  
	@Table — устанавливается на уровне класса и позволяет задать имя таблицы, каталог или схему базы данных, а также установить уникальные ключи. Если аннотация @Table не задана, то в качестве имени таблицы используется имя класса; - **необязательная аннотация**
[[Аннотация Column|@Column]] javax.persistence.Table  
	Через аннотацию @Column можно явно указать поведение столбца. Столбцу можно задавать такие свойства, как уникальность, обновляемость и т.д. **необязательная аннотация**
[[Аннотация Access|@Access]]
[[Аннотация Cacheable|@Cacheable]]

[[JoinColumn JoinTable]]
[[OrderrBy и OrderColumn]]
[[Аннотация Transient|@Transient]]

**Наименование полей**: По умолчанию JPA использует такой naming convention для полей и классов `fieldName` (в java) -> `field_name` (в БД).

Поэтому указывать в @Column(name = «another_name») имеет смысл, если это не так. 
@Id javax.persistence.Id  **обязательная аннотация**
	определяет простой (не составной) первичный ключ, состоящий из одного поля.
[[Аннотация GeneratedValue|@GeneratedValue]] javax.persistence.GeneratedValue  
@Version javax.persistence.Version  
@OrderBy javax.persistence.OrderBy
