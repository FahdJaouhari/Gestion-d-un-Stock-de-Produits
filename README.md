# Gestion-d-un-Stock-de-Produits
Gestion d'un Stock de Produits permet d'ajouter, mettre à jour, supprimer et consulter des articles en stock. Elle aide à suivre les quantités disponibles, à éviter les ruptures et à gérer efficacement l'inventaire.


#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <stdbool.h>
  typedef struct Date {
  char jour[3];
  char mois[3];
  char annee[5];
} Date;

typedef struct Produit {
  char Nom_produit[100];
  char reference[100];
  float Montant;
  Date dateAchat;
} Produit;

typedef struct Liste {
  Produit *produit;
  struct Liste *suiv;
} Liste;



Liste* creer_produit(char* nom, char* reference, float montant, Date dateAchat) {
     Liste* nouveau_noeud = (Liste*)malloc(sizeof(Liste));
     if (nouveau_noeud == NULL) {
        printf("Memory allocation failed!\n");
        return NULL;
     }

     nouveau_noeud->produit = (Produit*)malloc(sizeof(Produit));
    if (nouveau_noeud->produit == NULL) {
       printf("Memory allocation for product failed!\n");
       free(nouveau_noeud);
       return NULL;
   }

    strncpy(nouveau_noeud->produit->Nom_produit, nom, sizeof(nouveau_noeud->produit->Nom_produit) - 1);
    nouveau_noeud->produit->Nom_produit[sizeof(nouveau_noeud->produit->Nom_produit) - 1] = '\0';

    strncpy(nouveau_noeud->produit->reference, reference, sizeof(nouveau_noeud->produit->reference) - 1);
    nouveau_noeud->produit->reference[sizeof(nouveau_noeud->produit->reference) - 1] = '\0';


    nouveau_noeud->produit->Montant = montant;
    nouveau_noeud->produit->dateAchat = dateAchat;
    nouveau_noeud->suiv = NULL;

    return nouveau_noeud;
}


 void afficher_produit(Produit* produit) {
    if (produit == NULL) {
        printf("Invalid product\n");
        return;
    }
    printf("Nom du produit : %s\n", produit->Nom_produit);
    printf("Reference : %s\n", produit->reference);
    printf("Montant : %.2f\n", produit->Montant);
    printf("Date d'achat : %s/%s/%s\n", produit->dateAchat.jour, produit->dateAchat.mois, produit->dateAchat.annee);
    printf("-------------------\n");
}


  Liste* ajouter_debut(Liste* tete, Liste* nouveau_noeud) {
    nouveau_noeud->suiv = tete;
    return nouveau_noeud;
  }


  void ajouter_fin(Liste** tete, Liste* nouveau_noeud) {
     if (*tete == NULL) {
       *tete = nouveau_noeud;
     } else {
        Liste* temp = *tete;
        while (temp->suiv != NULL) {
           temp = temp->suiv;
        }
        temp->suiv = nouveau_noeud;
     }
  }


   void afficher_produits(Liste* tete) {
   Liste* temp = tete;
   while (temp != NULL) {
      afficher_produit(temp->produit);
      temp = temp->suiv;
   }
 }


      int longueur_liste(Liste* tete) {
   int count = 0;
   Liste* temp = tete;
   while(temp != NULL) {
      count++;
      temp = temp->suiv;
   }
    return count;
  }





bool dates_egal(Date d1, Date d2) {
    if(strcmp(d1.jour, d2.jour) == 0 &&
           strcmp(d1.mois, d2.mois) == 0 &&
           strcmp(d1.annee, d2.annee) == 0){
               printf("test");
           return true;
           }
           else{
               printf("%s",d1.jour);
                printf("test2\n");
            return false;
           }

}

void filtrer_date(Liste* tete, Date dt) {
  if (tete == NULL) {
      printf("Liste vide ou invalide\n");
      return;
  }

    Liste* temp = tete;
     int found = 0;

    while (temp != NULL) {
        if (dates_egal(temp->produit->dateAchat, dt)) {
            afficher_produit(temp->produit);
            found = 1;
        }
        temp = temp->suiv;
    }
    if (!found) {
        printf("Aucun produit trouve a cette date\n");
    }
}


  Liste* supprimer_debut(Liste* tete) {
    if (tete == NULL) {
        return NULL;
    }
    Liste* newHead = tete->suiv;
    free(tete->produit);
    free(tete);
    return newHead;
  }

  void supprimer_fin(Liste** tete) {
       if (*tete == NULL) {
           return;
       }
       if ((*tete)->suiv == NULL) {
           free((*tete)->produit);
           free(*tete);
           *tete = NULL;
           return;
       }

       Liste* temp = *tete;
       Liste* prev = NULL;
       while(temp->suiv != NULL) {
          prev = temp;
          temp = temp->suiv;
       }
       free(temp->produit);
       free(temp);
       prev->suiv = NULL;

  }

  Liste* rembourser(Liste* tete, char* ref) {
      if (tete == NULL) return NULL;

      if(strcmp(tete->produit->reference, ref) == 0) {
         return supprimer_debut(tete);
      }

      Liste *current = tete;
      Liste *prev = NULL;
      while(current != NULL && strcmp(current->produit->reference, ref) != 0) {
        prev = current;
        current = current->suiv;
      }

      if(current == NULL) {
          return tete;
      }

      prev->suiv = current->suiv;
      free(current->produit);
      free(current);
      return tete;
  }

       void trier_liste(Liste* tete, char* critere) {
            if (tete == NULL || tete->suiv == NULL) {
               return;
            }

          int swapped;
          Liste *ptr1;
          Liste *lptr = NULL;
           do {
               swapped = 0;
               ptr1 = tete;
               while (ptr1->suiv != lptr) {
                   if (strcmp(ptr1->produit->Nom_produit, ptr1->suiv->produit->Nom_produit) > 0) {
                       Produit* temp = ptr1->produit;
                       ptr1->produit = ptr1->suiv->produit;
                       ptr1->suiv->produit = temp;
                       swapped = 1;
                   }
                   ptr1 = ptr1->suiv;
               }
               lptr = ptr1;
           } while(swapped);
      }

    Liste* fusionner_listes(Liste* tete1, Liste* tete2) {
        if (tete1 == NULL) {
            return tete2;
        }
        if (tete2 == NULL) {
            return tete1;
        }

        Liste* temp = tete1;
        while(temp->suiv != NULL) {
           temp = temp->suiv;
        }

        temp->suiv = tete2;
        return tete1;
    }

      Liste*cloner_liste(Liste*tete){
        Liste* clonedHead = NULL;
        Liste* clonedTail = NULL;
         Liste* temp = tete;
          while (temp != NULL) {
               Liste* clonedNode = creer_produit(temp->produit->Nom_produit, temp->produit->reference, temp->produit->Montant, temp->produit->dateAchat);

               if (clonedHead == NULL) {
                    clonedHead = clonedNode;
                    clonedTail = clonedNode;
                } else {
                  clonedTail->suiv = clonedNode;
                  clonedTail = clonedNode;
                }
               temp = temp->suiv;
          }
          return clonedHead;
     }




     Liste* inserer_a_position(Liste* tete, Liste* nouveau_noeud, int position) {
        if (position < 0 ) return tete;

        if (position == 0) {
          nouveau_noeud->suiv = tete;
          return nouveau_noeud;
        }

         Liste* temp = tete;
         int count = 0;

         while (temp != NULL && count < position - 1) {
            temp = temp->suiv;
            count++;
         }

         if (temp == NULL) {
            return tete;
         }
         nouveau_noeud->suiv = temp->suiv;
         temp->suiv = nouveau_noeud;
        return tete;
     }


     int main() {
    //Sample Data
     Date date1 = {"01", "02", "2023"};
     Date date2 = {"02", "03", "2023"};
     Date date3 = {"22", "03", "2023"};


    Liste* liste_produits = NULL;

    Liste* prod1 = creer_produit("le bon poulet 0.60kg", "AXCF14V0VG8027", 48.33, date1);
    Liste* prod2 = creer_produit("yogo 1/2 L", "XXCF1400VG8666", 0.50, date2);
    Liste* prod3 = creer_produit("yogo 1/2 1", "chchgef848ddndn", 10.00, date3);

    ajouter_fin(&liste_produits, prod1);
    ajouter_fin(&liste_produits, prod2);
    ajouter_fin(&liste_produits, prod3);

    printf("Liste initiale:\n");
    afficher_produits(liste_produits);
     //Test filtrer
    printf("Filtrer les produits de la date 22/03/2023:\n");
     filtrer_date(liste_produits, date3);

     //Test fonction supprimer_debut
     liste_produits = supprimer_debut(liste_produits);
     printf("Liste apres suppression du premier:\n");
     afficher_produits(liste_produits);

     //Test supprimer fin
     supprimer_fin(&liste_produits);
     printf("Liste apres suppression du dernier:\n");
     afficher_produits(liste_produits);

      //Test ajouter début
    liste_produits = ajouter_debut(liste_produits, prod1);
    printf("Liste apres insertion en debut\n");
    afficher_produits(liste_produits);


     //Test rembourser
    liste_produits = rembourser(liste_produits, "AXCF14V0VG8027");
    printf("Liste apres remboursement\n");
    afficher_produits(liste_produits);

    //Test longueur
    int count = longueur_liste(liste_produits);
    printf("Longueur de la liste: %d\n", count);





    //Test fusionner
    Liste* liste2 = NULL;
    Liste* prod4 = creer_produit("test prod", "tst1234", 50, date1);
    ajouter_fin(&liste2, prod4);

    Liste* merged_list = fusionner_listes(liste_produits, liste2);
    printf("Liste apres fusion\n");
    afficher_produits(merged_list);

     //Test cloning
    Liste* cloned_list = cloner_liste(liste_produits);
    printf("Liste clonée:\n");
    afficher_produits(cloned_list);

    //Test trier
     printf("Liste apres le trie par nom:\n");
    trier_liste(liste_produits, "nom");
     afficher_produits(liste_produits);


    //Test inserer
     Liste* prod5 = creer_produit("Inserted Prod", "ins123", 30.00, date1);
     liste_produits = inserer_a_position(liste_produits, prod5, 1);
     printf("Liste apres l'insertion a position 1:\n");
     afficher_produits(liste_produits);

    return 0;
}

