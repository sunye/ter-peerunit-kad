On écrit des tests PeerUnit comme on écrit des tests unitaires. Une classe de test se décompose en méthodes qui seront exécutées successivement. Dans des tests unitaires classiques, toutes les méthodes sont exécutées. PeerUnit utilise un système d'annotations, qui permet de spécifier quel pair doit exécuter quelle méthode.

Avec un test unitaire :

```
class UnTestSimple {

Identifier id = newAbsId(1337);

@Before
public void setUp() {
    // Création de deux pairs sur la machine locale et connexion
    Kademlia kad1 = new Kademlia(Identifier.randomIdentifier(), 3333);
    Kademlia kad2 = new Kademlia(Identifier.randomIdentifier(), 4444);
    kad2.connect(new InetSocketAddress(InetAddress.getLocalHost(), 3333));
}

@Test
public testPutPair1() {
    kad1.put(id, "Bonjour");
    assertTrue(kad1.contains(id));
}

@Test
public testGetPair2() {
    String result = (String) kad2.get(id);
    assertEquals("Bonjour", result);
}

}
```

Avec PeerUnit :
```
class UnTestSimple {

private Identifier id = newAbsId(1337);
private final int port = 3333;

@Test(from=0, to=1, timeout=100000, name = "action1", step = 0)
public setUp() {
    Kademlia kad = new Kademlia(Identifier.randomIdentifier(), port+getPeerId());
}

@Test(place=1, timeout=100000, name = "action1", step = 0)
public setUp() {
    kad.connect(new InetSocketAddress(InetAddress.getLocalHost(), port));
}

@Test(place=0, timeout=100000, name = "action1", step = 1)
public testPut() {
    kad.put(id, "Bonjour");
    assertTrue(kad.contains(id));
}

@Test(place=1, timeout=100000, name = "action1", step = 2)
public testGet() {
    String result = (String) kad.get(id);
    assertEquals("Bonjour", result);
}

}
```

Ici, newAbsId est une méthode qui permet de générer un Identifer à partir d'un nombre.

Quand un pair démarre, il se connecte au coordinateur. Le coordinateur affecte un Id à chaque pair qui se connecte, le premier à l'id 0, le suivant 1 etc. La méthode getPeerId() permet à un pair de savoir l'Id qui lui a été attribué.

C'est ce même Id qui est utilisé dans les annotations dans place et from/to. Place permet d'indiquer qu'une méthode sera exécuté par un seul pair dont l'Id est préciser. From/to permet de faire exécuter la même méthode à chaque pair dont l'Id est compris entre les Id précisés dans l'annotation.

Avec name et step, on peut affecter l'ordre des méthodes mais nous n'avons pas fait usage de cette fonctionnalité par la suite.