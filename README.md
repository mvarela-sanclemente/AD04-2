
# Relación 1 a 1
Vamos ver como creamos unha relación 1 a 1.
No noso caso imos engadir ao noso equipo un entrenador, que é un obxecto.
Exemplo:

```java
import java.io.Serializable;
import javax.persistence.Column;
import javax.persistence.Entity;
import javax.persistence.Id;
import javax.persistence.Table;

@Entity
@Table(name="Entrenador")
public class Entrenador implements Serializable{
    
    @Id
    @Column(name="id")
    private int id;
    @Column(name="nome")
    private String nome;
    @Column(name="apelidos")
    private String apelidos;
    @Column(name="idade")
    private int idade;
    
    public Entrenador(){}
    
    public Entrenador(int id,String nome, String apelidos, int idade){
        this.id=id;
        this.nome= nome;
        this.apelidos=apelidos;
        this.idade = idade;
    }
}
```

Agora imos modificar a clase Equipo.


```java
import java.io.Serializable;
import javax.persistence.CascadeType;
import javax.persistence.Column;
import javax.persistence.Entity;
import javax.persistence.Id;
import javax.persistence.OneToOne;
import javax.persistence.PrimaryKeyJoinColumn;
import javax.persistence.Table;

@Entity
@Table(name="Equipo")
public class Equipo implements Serializable{
    
    @Id
    @Column(name="id")
    private int id;
    @Column(name="nome")
    private String nome;
    @Column(name="cidade")
    private String cidade;
    @Column(name="numeroSocios")
    private int numeroSocios;
    
    @OneToOne(cascade= CascadeType.ALL)
    @PrimaryKeyJoinColumn
    private Entrenador entrenador;
    
    public void Equipo(){
    }
    
    public Equipo(int id,String nome, String cidade, int numeroSocios, Entrenador entrenador){
        this.id = id;
        this.nome = nome;
        this.cidade = cidade;
        this.numeroSocios = numeroSocios;
        this.entrenador = entrenador;
    }
    
}
```
Neta clase o novo que temos é o atributo entrenador. O cal lle poñemos as seguintes anotacións:

- **@OneToOne(cascade=CascadeType.ALL)**: indica que a relación e 1 a 1 entre as dúas táboas. Indicámoslle tamén o valor de cascada. Así Hibernate saberá como ten que actuar cando se realicen as operacións de gardar, borrar, ler, etc. O valor all indica qque debemos realizar a mesma operación en Equipo que en Entrenador. (Ver sección correspondente para máis información.)
- **@PrimaryKeyJoinColumn**: Indicamos que a relación entre as dúas táboas se realiza mediante clave primaria.

Na clase HibernateUtil hai que engadirlle a liña corresponente para que se garde na base de datos a clase Entrenador.

```java
conf.addAnnotatedClass(Equipo.class);
conf.addAnnotatedClass(Entrenador.class);
```

Por último para realizar a relación 1 a 1 necesitamos que os **ids** de entrenador e equipo sexan os mesmos. 

```java
import org.hibernate.HibernateException;
import org.hibernate.Session;
import org.hibernate.Transaction;

public class Main {
    
    public static void main(String args[]){
        //Creamos un entrenador
        Entrenador entrenador = new Entrenador(1,"Nome1","Apelidos1",50);
        
        //Creamos un equipo
        Equipo equipo = new Equipo(1,"San Clemente", "Santiago", 1000, entrenador);
        Transaction tran = null;
        try{
            //Collemos a sesión de Hibernate
            Session session = HibernateUtil.getSessionFactory().openSession();
            //Comenzamos unha transacción
            tran = session.beginTransaction();
            
            //Gardamos o equipo
            session.save(equipo);
            
            //Facemos un commit da transacción
            tran.commit();
        }catch(HibernateException e){
            e.printStackTrace();
        }
    }
    
}
```
