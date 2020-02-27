# mapstruct

1. mapstruct-jdk8 & mapstruct
2. dto
3. mapper接口
4. usage：

```java
PeopleDTO peopleDTO = PeopleMapper.INSTANCE.entityToDTO(peopleEntity);

PeopleMapper.INSTANCE.updateEntityFromDto(newP, newEntity);
```

```java
@Mapper  
public interface PersonMapper {  

    PersonMapper INSTANCE = Mappers.getMapper(PersonMapper.class);  

    @Mapping(target = "personName", source = "name")  
    @Mapping(target = "hand",  ignore = true)  
     ..  
     ..  
     ..  
     ..  
    PersonModel map( Personentity);  

    List<PersonModel > map(List< Person> entity);  
}
```

不同的類型：

```java
public class UserTransform {

    public String booleanToString(boolean value){
        if(value){
            return "Y";
        }
        return "N";
    }

    public boolean strToBoolean(String str){
        if ("Y".equals(str)) {
            return true;
        }
        return false;
    }
}

@Mapper(uses = UserTransform.class)
public interface UserMapper {...}
```

