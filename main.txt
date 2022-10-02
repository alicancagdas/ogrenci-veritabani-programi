//EREN SEZER -200202025-//ALICAN CAGDAS -200202029-
#include<stdio.h>
#include<stdlib.h>
#include<io.h>
#include <unistd.h>
typedef struct ogrenci
{
    int s_id;
    int l_id;
    int s_marks;
    int adres;
} kayit;
int global=0;

void indeksDosyasiOlustur();
void kayitEkle();
void kayitSil();
void kayitGuncelle();
void kayitGoruntule();
void programArayuzu();
void indeksDosyasiniSil();

int main()
{       printf("\n\t\t\t*Ogrenci Veritabani Programina Hos Geldiniz*\n\n\n\n");
    kayit st1, st2;
    int pointer[100];

    int k=0;
    char *t;
    t= pointer;
    for(int a=0; a<=100; a++)
    {
        pointer[a]=k;
        k++;
    }
    FILE *fp;
    int secim, tempNo;
    fp = fopen("veri.bin", "a+b");
    fclose(fp);
    fp = fopen("index.txt", "a");
    fclose(fp);

    while (1)
    {
        programArayuzu();
        scanf("%d", &secim);
        switch (secim)
        {
        case 1:
            kayitEkle(&st1,&t);
            break;
        case 2:

            kayitGoruntule(&st2);
            break;
        case 3:
            printf("\n\nBulmak icin ogrenci no giriniz: ");
            scanf("%d", &tempNo);
            kayitBul(&st2, tempNo);
            break;
        case 4:
            printf("\n\nDuzenlemek icin ogrenci no giriniz: ");
            scanf("%d", &tempNo);
            kayitGuncelle(&st2, tempNo);
            break;
        case 5:
            printf("\n\nSilmek icin ogrenci no giriniz : ");
            scanf("%d", &tempNo);
            kayitSil(&st2, tempNo);
            break;
        case 6:{
            indeksDosyasiOlustur(&st2);
        }
        break;
        case 7:{
            indeksDosyasiniSil();
        }
            break;
        case 8:
            printf("Bu yazilimi kullandiginiz icin tesekkur eder iyi gunler dileriz..");
            exit(0);
        default:
            system("cls");
            printf("\n\nLutfen menu ekrani geldiginde '1'-'8' arasi bir numara giriniz !!!\n");
            sleep(3);
        }
    }
}

void indeksDosyasiOlustur(kayit *st2){
    FILE *index , *veri;

    index = fopen ("index.txt", "a+");
    veri = fopen ("veri.bin", "r+b");
    while ((fread( st2,16 , 1, veri))) {
           fwrite(&(st2->s_id), 4, 1, index);
           fwrite(&(st2->adres),4, 1, index);

    }
   _fcloseall();

}


void kayitEkle(kayit *st1,char *ptr)
{
    int n = 0;
    FILE *index, *veri;

    index = fopen("index.txt", "r+b");
    veri = fopen("veri.bin", "r+b");
    st1->adres=(ptr+global);
    global++;

    printf("\n\nOgrenci numarasini giriniz: ");
    scanf("%d", &st1->s_id);
    printf("\nDers kodunu giriniz: ");
    scanf("%d", &st1->l_id);
    printf("\nNotlari giriniz: ");
    scanf("%d", &st1->s_marks);
    fseek(veri, 0, SEEK_END);
    if (fwrite(st1, 16, 1, veri))
    {
        fseek(index, 0, SEEK_END);
        fwrite(&(st1->s_id), 4, 1, index);
        fwrite(&(st1->adres),4, 1, index);
    }
    else
    {
        printf("\n\nEklenemedi !!!");
    }


    fclose(index);
    fclose(veri);
        system("cls");
        printf("\nKayit Basarili!!\n");
}

void kayitGoruntule(kayit *st2)
{
    FILE *fp=NULL;
    int flag = 1, secim, r,adres;
    printf("\n\nLutfen asagidaki 2 secenekten birini seciniz..");
    printf("\n\n1:Index Dosyasi \n\n2:Veri Dosyasi\n\n==> ");
    scanf("%d", &secim);
    switch (secim)
    {
    case 1:
        fp = fopen("index.txt", "r");
        while (fread(&r, 4, 1, fp))
        {
            flag = 0;
            printf("\nOgrenci numarasi:%d ", r);
            fread(&adres, 4, 1, fp);
            printf("\nAdresi:%d \n---------------------\n", adres);
        }
        break;
    case 2:
        fp = fopen("veri.bin", "rb");
        while (fread(st2,16, 1, fp))
        {
            if ((st2->s_id) != 0)
            {
                if (flag == 1)
                    printf("\n\n  Ogrenci No	 Ders kodu	  Notlar        Adres\n----------------------------------------------------\n");
                flag = 0;
                printf("\n\n=>%d", st2->s_id);
                printf("	    \t %d", st2->l_id);
                printf("	  \t %d", st2->s_marks);
                printf("	  \t %d\n", st2->adres);
            }
        }

        break;

    default:

        printf("\n\nGecersiz giris !!!");
    }
if (flag == 1)
        printf("\n\nKayit Bulunamadi!!!");
    printf("\n\n");
    fclose(fp);

}
void kayitBul(kayit *st2, int gecicino)
{
    int r, konum, flag = 0;
    FILE *index, *veri;
    index = fopen("index.txt", "r+b");
    veri = fopen("veri.bin", "r+b");
    while (fread(&r, 4, 1, index))
    {
        fseek(index,4,SEEK_CUR);
        if (r == gecicino)
        {
            flag = 1;
            break;
        }
    }

    if (!flag)
    {
        printf("\n\nKayit Bulunamadi !!!");
    }
    else
    {
        fseek(index, -8, SEEK_CUR);
        konum = ftell(index);
        fseek(veri, konum*2, SEEK_SET);
        fread(st2, 16, 1, veri);

        printf("\n\nOgrenci Numarasi :%d", st2->s_id);
        printf("\n\nDers Kodu :%d", st2->l_id);
        printf("\n\nNotlar :%d", st2->s_marks);
        printf("\n\nPointer : %d",st2->adres);
    }

    printf("\n\n");
    fclose(index);
    fclose(veri);

}
void kayitGuncelle(kayit *st2, int gecicino)
{
    int r, konum, flag = 0;
    FILE *index, *veri;

    index = fopen("index.txt", "r+b");
    veri = fopen("veri.bin", "r+b");

    while (fread(&r, 4, 1, index))
    {
        fseek(index,4,SEEK_CUR);
        if (r == gecicino)
        {
            flag = 1;

            break;
        }
    }
    if (!flag)
    printf("\n\nKayit Bulunamadi! !!!");

    else
    {
        fseek(index, (-8), SEEK_CUR);
        konum = ftell(index);
        fseek(veri, konum *2, SEEK_SET);
        fread(st2, 16, 1, veri);

        printf("\n\nOgrenci Numarasi :%d", st2->s_id);
        printf("\n\nDers kodu :%d", st2->l_id);
        printf("\n\nNotlar :%d", st2->s_marks);
        printf("\nYeni notlari giriniz : ");
        scanf("%d", &st2->s_marks);


        fseek(veri, (-16), SEEK_CUR);
        fwrite(st2, 16, 1, veri);
    }

    printf("\n\n");
    fclose(index);
    fclose(veri);

}
void kayitSil(kayit *st2, int gecicino)
{
    int r, konum, flag = 0,veridtutucu,veridboyut;
    char secim;

    FILE *index, *veri;

    index = fopen("index.txt", "r+b");
    veri = fopen("veri.bin", "r+b");

    veridtutucu=_fileno(veri);
    veridboyut=_filelength(veridtutucu);

    while (fread(&r, 4, 1, index))
    {
        fseek(index,4,SEEK_CUR);
        if (r == gecicino)
        {
            flag = 1;

            break;
        }
    }
    if (!flag)
    {
        printf("\n\nKayit Bulunamadi !!!");
    }
    else
    {
        fseek(index, (-8), SEEK_CUR);
        konum = ftell(index);

        fseek(veri, konum *2, SEEK_SET);
        fread(st2, 16, 1, veri);

        printf("\n\nOgrenci Numarasi :%d", st2->s_id);
        printf("\n\nDers Kodu :%d", st2->l_id);
        printf("\n\nNotlar :%d", st2->s_marks);
        printf("\n\nPointer : %d",st2->adres);

        printf("\n\nKaydi silmek ister misiniz ? (E/H)");
        fflush(stdin);
        scanf("%c", &secim);

        if (secim == 'h' || secim == 'H')
            return;


            while (fread(st2, 16, 1, veri))
        {
            fseek(veri, (-32), SEEK_CUR);
            fwrite(st2, 16, 1, veri);
            fseek(veri, 16, SEEK_CUR);
        }
    }

    _chsize(veridtutucu,veridboyut-16);

    printf("\n\n");

    _fcloseall();
remove("index.txt");
if (flag){
    indeksDosyasiOlustur(&st2);
}



}
void indeksDosyasiniSil(){
    char onay;
printf("Dosyayi silmek istiyor musunuz?(E/H)");
  onay=getche();
if(toupper(onay)=='E'||toupper(onay)=='e'){


    if(remove("index.txt")==0){
        printf("\nKayit basariyla silindi..\n");
    }

}
}
void programArayuzu(){
     system("color fc");
    printf("\t\t\t\t------------------------------- \n");
    printf("\t\t\t\t|| 1. Kayit Ekle              ||\n");
    printf("\t\t\t\t|| 2. Kayit Goruntule         ||\n");
    printf("\t\t\t\t|| 3. Kayit Bul               ||\n");
    printf("\t\t\t\t|| 4. Kayit Guncelle          ||\n");
    printf("\t\t\t\t|| 5. Kayit Sil               ||\n");
    printf("\t\t\t\t|| 6. Indeks Dosyasi Olustur  ||\n");
    printf("\t\t\t\t|| 7. Indeks Dosyasi Sil      ||\n");
    printf("\t\t\t\t|| 8. EXIT                    ||\n");

    printf("\t\t\t\t-------------------------------\n");
    printf("\n\t\t==>");
}
