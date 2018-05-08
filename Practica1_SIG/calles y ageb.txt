--Tarea Practica 1
	--Ejercicio 1

	--Calles
	--Validación y conversión de formao de 4326 a 32614
   ALTER TABLE calles
   ALTER COLUMN geom
   TYPE Geometry(Polygon, 32614)
   USING ST_Transform(geom, 32614);

	-- Corte calles con limite metropolitano 
	create table calles_zmvm as(
	select calles.*
	from calles
	inner join limite_metropolitano on
	st_intersects(limite_metropolitano.geom,calles.geom)
	)

	-- Creación de índice espacial del Corte
	create index calles_zmvm_gix on calles_zmvm using GIST(geom);

	-- Validación para PK (Unicidad)
	select count(*) from calles_zmvm  group by id order by count(*) desc;

	-- Transformación a id's unicos
	update calles_zmvm set id = nextval('"calles_zmvm_id_seq"');

	-- Agregar restricciones a la columna "id" del corte (No nulos, unicidad y finalmente PK)
	ALTER TABLE calles_zmvm ALTER COLUMN "id" SET NOT NULL;
	ALTER TABLE calles_zmvm ADD UNIQUE ("id");
	ALTER TABLE calles_zmvm ADD PRIMARY KEY ("id");

	--AGEB
	--Validación y conversión de formao de 4326 a 32614
   ALTER TABLE ageb
   ALTER COLUMN geom
   TYPE Geometry(Polygon, 32614)
   USING ST_Transform(geom, 32614);

	-- Corte ageb con limite metropolitano 
	create table ageb_zmvm as(
	select ageb.*
	from ageb
	inner join limite_metropolitano on
	st_intersects(limite_metropolitano.geom,ageb.geom)
	)

	-- Creación de índice espacial del Corte
	create index ageb_zmvm_gix on ageb_zmvm using GIST(geom);

	-- Validación para PK (Unicidad)
	select count(*) from ageb_zmvm  group by id order by count(*) desc;

	-- Transformación a id's unicos
	update ageb_zmvm set id = nextval('"ageb_zmvm_id_seq"');

	-- Agregar restricciones a la columna "id" del corte (No nulos, unicidad y finalmente PK)
	ALTER TABLE ageb_zmvm ALTER COLUMN "id" SET NOT NULL;
	ALTER TABLE ageb_zmvm ADD UNIQUE ("id");
	ALTER TABLE ageb_zmvm ADD PRIMARY KEY ("id");

	--Ejercicio 2
	
	--calles
	create index calles_zmvm_gix on calles_zmvm using GIST(geom);

	--ageb
	create index ageb_zmvm_gix on ageb_zmvm using GIST(geom);

	--Ejercicio 3

	--calles
	select  calles_zmvm.id, calles_zmvm.cvegeo, colonias.id_colonia,calles_zmvm.geom
	into calles_colonias
	from calles_zmvm join colonias on st_intersects(colonias.geom, calles_zmvm.geom);

	--ageb
	select  ageb_zmvm.id, ageb_zmvm.cvegeo, colonias.id_colonia,ageb_zmvm.geom
	into ageb_colonias
	from ageb_zmvm join colonias on st_intersects(colonias.geom, ageb_zmvm.geom);

