# Laboratorul-5
Ursu Cristian W-1842

import java.io.IOException;
import java.util.ArrayList;
import java.util.InputMismatchException;
import java.util.Scanner;

public class Main {
    static ArrayList<Animal> animale = null;

    public static void main(String[] args) throws IOException {
        animale = Fisier.citesteAnimale();
        afiseazaMeniu();
    }
  
/** acesta este meniul, putem selecta urmatoarele operatiuni pentru a fi efectuate */
  
    static void afiseazaMeniu() throws IOException {
        int urmatoarea = 0;
        do {
            System.out.println("1. Afiseaza animalele");
            System.out.println("2. Introdu un animal nou de companie");
            System.out.println("3. Sterge animal");
            System.out.println("4. Cheama animal");
            System.out.println("5. Câtă mâncare trebuie");
            System.out.println("6. Toate striga-n cor");
            System.out.println("7. Iesi");

            urmatoarea = citesteOptiune(1, 7);
            switch (urmatoarea) {
                case 1:
                    afiseazaAnimalele(false);
                    break;
                case 2:
                    introduAnimalNou();
                    break;
                case 3:
                    stergeAnimal();
                    break;
                case 4:
                    cheamaAnimal();
                    break;
                case 5:
                    mancareDeCumparat();
                    break;
                case 6:
                    toateStriga();
                    break;
                case 7:
                    break;
            }
        } while (urmatoarea != 7);
    }
  
/**aceasta functie va afisa animalele declarate in tabloul Animal dupa anumiti parametri, iar in cazul in care nu sunt animale declarate aceasta functie va returna ca nici un animal nu a fost definit */
  
    static ArrayList<Animal> afiseazaAnimalele(boolean chemabile) {
        if (animale.isEmpty()) {
            System.out.println("Nu e definit nici un animal");
            System.out.println();
            return new ArrayList<>();
        }

        int indice = 1;
        ArrayList<Animal> animaleCopie = new ArrayList<>(animale);
        if (chemabile) {
            animaleCopie.removeIf(elem -> !(elem instanceof Chemabila));
        }
        for (Animal animal: animaleCopie) {
            System.out.println(indice + ": " + animal);
            indice++;
        }
        System.out.println();
        return animaleCopie;
    }
  
/** aceasta functie va introduce un nou animal in fisierul 'animale', fiecare animal are anumite atribute unice, in baza carora acestea vor fi identificate in momentul cautarii lor, datele despre animal trebuie introduse corect, adica, numele sa fie scris cu litere, greutatea declarata cu cifre etc, in caz contrar vor aparea erori de definire */
  
  
    static void introduAnimalNou() throws IOException {
        int optiune = 0;

        do {
            System.out.println("Ce animal doriti sa introduceti?");
            System.out.println("0. Anulare");
            System.out.println("1. Caine");
            System.out.println("2. Pisica");
            System.out.println("3. Papagal");

            Scanner scanner = new Scanner(System.in);
            optiune = citesteOptiune(0, 3);

            if (optiune == 0)
                continue;

            System.out.println("Introduceti numele animalului, greutatea sa si cat mananca pe saptamana, in kg, separate prin spatiu");
            String nume = null;
            int greutate = 0;
            float manancaPeSapt = 0;

            while (true) {
                try {
                    nume = scanner.next();
                    greutate = scanner.nextInt();
                    manancaPeSapt = scanner.nextFloat();

                } catch (InputMismatchException ex) {
                    System.out.println("Datele au fost eronate, incercati din nou");
                    continue;
                }
                break;
            }

            Animal animal = null;
            switch (optiune) {
                case 1:
                    animal = new Caine(nume, greutate, manancaPeSapt);
                    break;
                case 2:
                    animal = new Pisica(nume, greutate, manancaPeSapt);
                    break;
                case 3:
                    animal = new Papagal(nume, greutate, manancaPeSapt);
                    break;
                default:
                    break;
            }

            animale.add(animal);
        } while (optiune != 0);

        Fisier.scrieAnimale(animale);
        System.out.println();
    }

  /** aceasta functie are ca scop stergerea unui animal din fisier, stergerea se va efectua dupa numele animalului */
  
    static void stergeAnimal() {
        if (afiseazaAnimalele(false).isEmpty()) return;
        System.out.println("Alegeti animalul ce trebuie sters");
        int deSters = citesteOptiune(1, animale.size());
        animale.remove(deSters-1);
        System.out.println();
    }

  /** aceasta functie va chema animalul dorit, evident daca acesta va fi gasit in fisier */
  
    static void cheamaAnimal() {
        ArrayList<Animal> animaleDeChemat = afiseazaAnimalele(true);
        if (animaleDeChemat.isEmpty()) return;

        System.out.println("Pe care animal doriti sa-l chemati?");
        int deChemat = citesteOptiune(1, animaleDeChemat.size());
        Animal animalChemat = animaleDeChemat.get(deChemat-1);
        ((Chemabila)animalChemat).cheama();
        System.out.println();
    }
  
  /** aceasta functie va calcula exact mancarea care va fi consumata de animalul selectat */
  
    static void mancareDeCumparat() {
        float suma = 0;
        for (Animal animal: animale) {
            suma += animal.mancarePeSaptamana;
        }
        System.out.println(String.format("Trebuie de cumparat %.2f kg de mancare.", suma));
        System.out.println();
    }

  /** aceasta functie va face ca fiecare animal sa strige in felul sau, la declararea animalelor, s-a utilizat functia striga, anume in aceasta functie i se atribuie fiecarui animal modul sau unic de a striga, spre exemplu, cainele striga: 'Ham-ham' */
  
    static void toateStriga() {
        ArrayList<Glasuitoare> strigatoare = (ArrayList<Glasuitoare>)(ArrayList<?>)animale;
        for (Glasuitoare glasuitor: strigatoare) {
            glasuitor.striga();
        }
        System.out.println();
    }
    
/** aceasa functie va citi instructiunea din meniu, daca numarul optiunii se va regasi in meniu, aceasta va fi efectuata, iar daca numarul nu este unul existent, se va afisa mesajul ca optiunea este una nevalida */

    static int citesteOptiune(int deLa, int panaLa) {
        int optiune;
        Scanner scanner = new Scanner(System.in);
        optiune = scanner.nextInt();

        while (optiune < deLa || optiune > panaLa) {
            System.out.println(String.format("Optiune invalida. Introduceti o cifra de la %d la %d", deLa, panaLa));
            optiune = scanner.nextInt();
        }

        return optiune;
    }
}





Aceasta secventa de cod este doar main-ul intregului program, este o secventa complet functionala. Pe langa acest main, mai exista fisierul Animale.txt, Animal.java, Caine.java, Chemabila.java, Fisier.java, Glasuitoare.java, Papagal.java, Pisica.java.
