# PostgreSQL extensions

## UUID
create extension if not exist "uuid-ossp";

uuid_generate_v1()

insert into student (id, name, course) values (uuid_generate(), 'abc', 'rust');

select uuid_generate_v1();
