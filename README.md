# <font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Meilleures pratiques pour une application angulaire propre et performante</font></font>

<font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Cet article décrit les pratiques que nous utilisons dans notre application et concerne Angular, Typescript, RxJs et @ ngrx / store.</font> <font style="vertical-align: inherit;">Nous allons également passer en revue certaines directives générales de codage pour aider à rendre l'application plus propre.</font></font>

#### **<font style="vertical-align: inherit;"><font style="vertical-align: inherit;">1) trackBy</font></font>**

<font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Lorsque vous utilisez</font></font> `ngFor`<font style="vertical-align: inherit;"><font style="vertical-align: inherit;">pour parcourir en boucle un tableau dans les modèles, utilisez-le avec une</font></font> `trackBy`<font style="vertical-align: inherit;"><font style="vertical-align: inherit;">fonction qui renverra un identifiant unique pour chaque élément.</font></font>

**<font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Pourquoi?</font></font>**

<font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Lorsqu'un tableau est modifié, Angular restitue l'ensemble de l'arborescence DOM.</font> <font style="vertical-align: inherit;">Mais si vous utilisez</font></font> `trackBy`<font style="vertical-align: inherit;"><font style="vertical-align: inherit;">, Angular saura quel élément a été modifié et n'apportera que des modifications du DOM pour cet élément en particulier.</font></font>

<font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Pour une explication détaillée à ce sujet, veuillez vous reporter à</font></font> [<font style="vertical-align: inherit;"><font style="vertical-align: inherit;">cet article</font></font>](https://netbasal.com/angular-2-improve-performance-with-trackby-cc147b5104e5) <font style="vertical-align: inherit;"><font style="vertical-align: inherit;">de</font></font> [<font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Netanel Basal</font></font>](undefined) <font style="vertical-align: inherit;"><font style="vertical-align: inherit;">.</font></font>

**<font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Avant</font></font>**

    <li *ngFor="let item of items;">{{ item }}</li>

**<font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Après</font></font>**

    // in the template

    <li *ngFor="let item of items; trackBy: trackByFn">{{ item }}</li>

    // in the component

    trackByFn(index, item) {    
       return item.id; // unique id corresponding to the item
    }

#### **<font style="vertical-align: inherit;"><font style="vertical-align: inherit;">2) const vs let</font></font>**

<font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Lors de la déclaration de variables, utilisez const lorsque la valeur ne sera pas réaffectée.</font></font>

**<font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Pourquoi?</font></font>**

<font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Utiliser</font></font> `let`<font style="vertical-align: inherit;"><font style="vertical-align: inherit;">et, le</font></font> `const`<font style="vertical-align: inherit;"><font style="vertical-align: inherit;">cas échéant, clarifie l’intention des déclarations.</font> <font style="vertical-align: inherit;">Cela aidera également à identifier les problèmes lorsqu'une valeur est réaffectée accidentellement à une constante en générant une erreur de temps de compilation.</font> <font style="vertical-align: inherit;">Cela contribue également à améliorer la lisibilité du code.</font></font>

**<font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Avant</font></font>**

    let car = 'ludicrous car';

    let myCar = `My ${car}`;
    let yourCar = `Your ${car};

    if (iHaveMoreThanOneCar) {
       myCar = `${myCar}s`;
    }

    if (youHaveMoreThanOneCar) {
       yourCar = `${youCar}s`;
    }

**<font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Après</font></font>**

    // the value of car is not reassigned, so we can make it a const
    const car = 'ludicrous car';

    let myCar = `My ${car}`;
    let yourCar = `Your ${car};

    if (iHaveMoreThanOneCar) {
       myCar = `${myCar}s`;
    }

    if (youHaveMoreThanOneCar) {
       yourCar = `${youCar}s`;
    }

#### <font style="vertical-align: inherit;"><font style="vertical-align: inherit;">3) opérateurs canalisés</font></font>

<font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Utilisez des opérateurs canalisables lorsque vous utilisez des opérateurs RxJs.</font></font>

**<font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Pourquoi?</font></font>**

<font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Les opérateurs pipeables sont arborescents, ce qui signifie que seul le code que nous devons exécuter sera inclus lors de leur importation.</font></font>

<font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Cela facilite également l'identification des opérateurs non utilisés dans les fichiers.</font></font>

_<font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Remarque:</font></font>_ <font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Cela nécessite la version 5.5 + angulaire.</font></font>

**<font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Avant</font></font>**

    import 'rxjs/add/operator/map';
    import 'rxjs/add/operator/take';

    iAmAnObservable
        .map(value => value.item)
        .take(1);

**<font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Après</font></font>**

    import { map, take } from 'rxjs/operators';

    iAmAnObservable
        .pipe(
           map(value => value.item),
           take(1)
         );

#### <font style="vertical-align: inherit;"><font style="vertical-align: inherit;">4) Isoler les hacks de l'API</font></font>

<font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Toutes les API ne sont pas à l'épreuve des balles - nous avons parfois besoin d'ajouter une certaine logique dans le code pour remédier aux bogues dans les API.</font> <font style="vertical-align: inherit;">Au lieu d’avoir les hacks dans les composants où ils sont nécessaires, il est préférable de les isoler en un seul endroit - comme dans un service et d’utiliser le service à partir du composant.</font></font>

**<font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Pourquoi?</font></font>**

<font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Cela aide à garder les hacks «plus proches de l'API», aussi près que possible de l'endroit où la requête réseau est faite.</font> <font style="vertical-align: inherit;">De cette façon, moins de votre code traite du code non piraté.</font> <font style="vertical-align: inherit;">En outre, c’est un endroit où vivent tous les hacks et il est plus facile de les trouver.</font> <font style="vertical-align: inherit;">Lorsque vous corrigez les bogues dans les API, il est plus facile de les rechercher dans un seul fichier plutôt que de rechercher les hacks pouvant être répartis dans la base de code.</font></font>

<font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Vous pouvez également créer des balises personnalisées, telles que API_FIX, similaires à TODO, et baliser les correctifs avec elle afin de faciliter la recherche.</font></font>

#### <font style="vertical-align: inherit;"><font style="vertical-align: inherit;">5) S'abonner au modèle</font></font>

<font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Évitez de vous abonner à des observables à partir de composants, mais souscrivez plutôt aux observables à partir du modèle.</font></font>

**<font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Pourquoi?</font></font>**

`async`<font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Les tubes se désabonnent automatiquement et cela simplifie le code en éliminant la nécessité de gérer manuellement les abonnements.</font> <font style="vertical-align: inherit;">Cela réduit également le risque d'oubli accidentel de la désabonnement d'un composant dans le composant, ce qui provoquerait une fuite de mémoire.</font> <font style="vertical-align: inherit;">Ce risque peut également être atténué en utilisant une règle de protection pour détecter les éléments observables non souscrits.</font></font>

<font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Cela empêche également les composants d'être dynamiques et d'introduire des bogues où les données sont mutées en dehors de l'abonnement.</font></font>

**<font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Avant</font></font>**

    // // template

    <p>{{ textToDisplay }}</p>

    // component

    iAmAnObservable
        .pipe(
           map(value => value.item),
           takeUntil(this._destroyed$)
         )
        .subscribe(item => this.textToDisplay = item);

**<font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Après</font></font>**

    // template

    <p>{{ textToDisplay$ | async }}</p>

    // component

    this.textToDisplay$ = iAmAnObservable
        .pipe(
           map(value => value.item)
         );

#### **<font style="vertical-align: inherit;"><font style="vertical-align: inherit;">6) Nettoyer les abonnements</font></font>**

<font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Lors de la</font> <font style="vertical-align: inherit;">souscription à des</font> <font style="vertical-align: inherit;">observables, assurez -</font> <font style="vertical-align: inherit;">vous toujours vous vous désabonnez de manière appropriée en utilisant des</font> <font style="vertical-align: inherit;">opérateurs comme</font></font> `take`<font style="vertical-align: inherit;"><font style="vertical-align: inherit;">,</font></font> `takeUntil`<font style="vertical-align: inherit;"><font style="vertical-align: inherit;">, etc.</font></font>

**<font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Pourquoi?</font></font>**

<font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Si vous ne vous désabonnez pas des données observables, des fuites de mémoire indésirables se produiront si le flux d'observables reste ouvert, même après la destruction d'un composant ou la navigation de l'utilisateur vers une autre page.</font></font>

<font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Mieux encore, créez une règle anti-peluches pour détecter les éléments observables non abonnés.</font></font>

**<font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Avant</font></font>**

    iAmAnObservable
        .pipe(
           map(value => value.item)     
         )
        .subscribe(item => this.textToDisplay = item);

**<font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Après</font></font>**

<font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Utiliser</font></font> `takeUntil`<font style="vertical-align: inherit;"><font style="vertical-align: inherit;">quand vous voulez écouter les changements jusqu'à ce qu'un autre observable émette une valeur:</font></font>

    private _destroyed$ = new Subject();

    public ngOnInit (): void {
        iAmAnObservable
        .pipe(
           map(value => value.item)
          // We want to listen to iAmAnObservable until the component is destroyed,
           takeUntil(this._destroyed$)
         )
        .subscribe(item => this.textToDisplay = item);
    }

    public ngOnDestroy (): void {
        this._destroyed$.next();
        this._destroyed$.complete();
    }

<font style="vertical-align: inherit;"><font style="vertical-align: inherit;">L'utilisation d'un sujet privé comme celui-ci constitue un modèle permettant de gérer la désinscription de nombreux observables dans le composant.</font></font>

<font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Utiliser</font></font> `take`<font style="vertical-align: inherit;"><font style="vertical-align: inherit;">quand vous voulez seulement la première valeur émise par l'observable:</font></font>

    iAmAnObservable
        .pipe(
           map(value => value.item),
           take(1),
           takeUntil(this._destroyed$)
        )
        .subscribe(item => this.textToDisplay = item);

<font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Notez l'utilisation de</font></font> `takeUntil`<font style="vertical-align: inherit;"><font style="vertical-align: inherit;">avec</font></font> `take`<font style="vertical-align: inherit;"><font style="vertical-align: inherit;">ici.</font> <font style="vertical-align: inherit;">Cela permet d'éviter les fuites de mémoire lorsque l'abonnement n'a pas reçu de valeur avant la destruction du composant.</font> <font style="vertical-align: inherit;">Sans cette</font></font> `takeUntil`<font style="vertical-align: inherit;"><font style="vertical-align: inherit;">option, l'abonnement resterait en attente jusqu'à ce qu'il obtienne la première valeur, mais comme le composant est déjà détruit, il ne recevra jamais de valeur, ce qui entraînerait une fuite de mémoire.</font></font>

#### <font style="vertical-align: inherit;"><font style="vertical-align: inherit;">7) Utiliser des opérateurs appropriés</font></font>

<font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Lorsque vous utilisez des opérateurs d'aplatissement avec vos observables, utilisez l'opérateur approprié à la situation.</font></font>

_<font style="vertical-align: inherit;"><font style="vertical-align: inherit;">switchMap:</font></font>_ <font style="vertical-align: inherit;"><font style="vertical-align: inherit;">quand vous voulez ignorer les émissions précédentes quand il y a une nouvelle émission</font></font>

_<font style="vertical-align: inherit;"><font style="vertical-align: inherit;">mergeMap:</font></font>_ <font style="vertical-align: inherit;"><font style="vertical-align: inherit;">quand vous voulez gérer simultanément toutes les émissions</font></font>

_<font style="vertical-align: inherit;"><font style="vertical-align: inherit;">concatMap:</font></font>_ <font style="vertical-align: inherit;"><font style="vertical-align: inherit;">quand vous voulez gérer les émissions l'une après l'autre au fur et à mesure de leur émission</font></font>

_<font style="vertical-align: inherit;"><font style="vertical-align: inherit;">exhaustMap:</font></font>_ <font style="vertical-align: inherit;"><font style="vertical-align: inherit;">lorsque vous souhaitez annuler toutes les nouvelles émissions tout en traitant une émission précédente</font></font>

<font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Pour une explication plus détaillée à ce sujet, veuillez vous référer à</font></font> [<font style="vertical-align: inherit;"><font style="vertical-align: inherit;">cet</font></font>](https://blog.angularindepth.com/switchmap-bugs-b6de69155524) <font style="vertical-align: inherit;"><font style="vertical-align: inherit;">article de</font></font> [<font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Nicholas Jamieson</font></font>](undefined) <font style="vertical-align: inherit;"><font style="vertical-align: inherit;">.</font></font>

**<font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Pourquoi?</font></font>**

<font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Utiliser un seul opérateur lorsque cela est possible au lieu de chaîner plusieurs autres opérateurs pour obtenir le même effet peut entraîner l'envoi de moins de code à l'utilisateur.</font> <font style="vertical-align: inherit;">L'utilisation d'opérateurs incorrects peut entraîner un comportement indésirable, car différents opérateurs traitent les observables de différentes manières.</font></font>

#### <font style="vertical-align: inherit;"><font style="vertical-align: inherit;">8) charge paresseuse</font></font>

<font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Dans la mesure du possible, essayez de charger paresseusement les modules dans votre application Angular.</font> <font style="vertical-align: inherit;">Le chargement différé consiste à charger quelque chose uniquement lorsqu'il est utilisé, par exemple à charger un composant uniquement lorsqu'il doit être vu.</font></font>

**<font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Pourquoi?</font></font>**

<font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Cela réduira la taille de l'application à charger et peut améliorer le temps de démarrage de l'application en ne chargeant pas les modules non utilisés.</font></font>

**<font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Avant</font></font>**

    // app.routing.ts

    { path: 'not-lazy-loaded', component: NotLazyLoadedComponent }

**<font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Après</font></font>**

    // app.routing.ts

    { 
      path: 'lazy-load',
      loadChildren: 'lazy-load.module#LazyLoadModule' 
    }

    // lazy-load.module.ts
    import { NgModule } from '@angular/core';
    import { CommonModule } from '@angular/common';
    import { RouterModule } from '@angular/router';
    import { LazyLoadComponent }   from './lazy-load.component';

    @NgModule({
      imports: [
        CommonModule,
        RouterModule.forChild([
             { 
                 path: '',
                 component: LazyLoadComponent 
             }
        ])
      ],
      declarations: [
        LazyLoadComponent
      ]
    })
    export class LazyModule {}

#### <font style="vertical-align: inherit;"><font style="vertical-align: inherit;">9) Évitez d'avoir des abonnements à l'intérieur d'abonnements</font></font>

<font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Parfois, vous voudrez peut-être utiliser les valeurs de plusieurs valeurs pour effectuer une action.</font> <font style="vertical-align: inherit;">Dans ce cas, évitez de vous abonner à un observable dans le bloc d'abonnement d'un autre observable.</font> <font style="vertical-align: inherit;">Utilisez plutôt des opérateurs de chaînage appropriés.</font> <font style="vertical-align: inherit;">Les opérateurs de chaînage fonctionnent sur des observables de l'opérateur qui les précède.</font> <font style="vertical-align: inherit;">Certains opérateurs sont enchaînant:</font></font> `withLatestFrom`<font style="vertical-align: inherit;"><font style="vertical-align: inherit;">,</font></font> `combineLatest`<font style="vertical-align: inherit;"><font style="vertical-align: inherit;">, etc.</font></font>

**<font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Avant</font></font>**

    firstObservable$.pipe(
       take(1)
    )
    .subscribe(firstValue => {
        secondObservable$.pipe(
            take(1)
        )
        .subscribe(secondValue => {
            console.log(`Combined values are: ${firstValue} & ${secondValue}`);
        });
    });

**<font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Après</font></font>**

    firstObservable$.pipe(
        withLatestFrom(secondObservable$),
        first()
    )
    .subscribe(([firstValue, secondValue]) => {
        console.log(`Combined values are: ${firstValue} & ${secondValue}`);
    });

**<font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Pourquoi?</font></font>**

_<font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Odeur de code / lisibilité / complexité</font></font>_ <font style="vertical-align: inherit;"><font style="vertical-align: inherit;">: ne pas utiliser pleinement les fichiers RxJ, suggère au développeur de ne pas connaître la surface de l'API RxJs.</font></font>

_<font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Performance</font></font>_ <font style="vertical-align: inherit;"><font style="vertical-align: inherit;">: Si les observables sont froides, il s’abonnera à firstObservable, attendez qu’il soit terminé, puis lancez le travail du deuxième observable.</font> <font style="vertical-align: inherit;">S'il s'agissait de demandes de réseau, le résultat serait synchrone / cascade.</font></font>

#### <font style="vertical-align: inherit;"><font style="vertical-align: inherit;">10) éviter tout;</font> <font style="vertical-align: inherit;">tapez tout;</font></font>

<font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Déclarez toujours les variables ou les constantes avec un type autre que</font></font> `any`<font style="vertical-align: inherit;"><font style="vertical-align: inherit;">.</font></font>

**<font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Pourquoi?</font></font>**

<font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Lors de la déclaration de variables ou de constantes dans Typescript sans saisie, la saisie de la variable / constante est déduite de la valeur qui lui est affectée.</font> <font style="vertical-align: inherit;">Cela causera des problèmes inattendus.</font> <font style="vertical-align: inherit;">Un exemple classique est:</font></font>

    const x = 1;
    const y = 'a';
    const z = x + y;

    console.log(`Value of z is: ${z}`

    // Output
    Value of z is 1a

<font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Cela peut entraîner des problèmes indésirables lorsque vous vous attendez à ce qu’un nombre soit également défini.</font> <font style="vertical-align: inherit;">Ces problèmes peuvent être évités en tapant les variables de manière appropriée.</font></font>

    const x: number = 1;
    const y: number = 'a';
    const z: number = x + y;

    // This will give a compile error saying:

    Type '"a"' is not assignable to type 'number'.

    const y:number

<font style="vertical-align: inherit;"><font style="vertical-align: inherit;">De cette façon, nous pouvons éviter les bugs causés par des types manquants.</font></font>

<font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Un bon avantage à avoir de bonnes frappe dans votre application est que cela rend le refactoring plus facile et plus sûr.</font></font>

<font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Considérons cet exemple:</font></font>

    public ngOnInit (): void {
        let myFlashObject = {
            name: 'My cool name',
            age: 'My cool age',
            loc: 'My cool location'
        }
        this.processObject(myFlashObject);
    }

    public processObject(myObject: any): void {
        console.log(`Name: ${myObject.name}`);
        console.log(`Age: ${myObject.age}`);
        console.log(`Location: ${myObject.loc}`);
    }

    // Output
    Name: My cool name
    Age: My cool age
    Location: My cool location

<font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Disons que</font> <font style="vertical-align: inherit;">nous voulons renommer la propriété</font></font> `loc`<font style="vertical-align: inherit;"><font style="vertical-align: inherit;">à</font></font> `location`<font style="vertical-align: inherit;"><font style="vertical-align: inherit;">en</font></font> `myFlashObject`<font style="vertical-align: inherit;"><font style="vertical-align: inherit;">:</font></font>

    public ngOnInit (): void {
        let myFlashObject = {
            name: 'My cool name',
            age: 'My cool age',
            location: 'My cool location'
        }
        this.processObject(myFlashObject);
    }

    public processObject(myObject: any): void {
        console.log(`Name: ${myObject.name}`);
        console.log(`Age: ${myObject.age}`);
        console.log(`Location: ${myObject.loc}`);
    }

    // Output
    Name: My cool name
    Age: My cool age
    Location: undefined

<font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Si nous n'avons pas de taper sur</font></font> `myFlashObject`<font style="vertical-align: inherit;"><font style="vertical-align: inherit;">, il pense que la propriété</font></font> `loc`<font style="vertical-align: inherit;"><font style="vertical-align: inherit;">sur</font></font> `myFlashObject`<font style="vertical-align: inherit;"><font style="vertical-align: inherit;">est tout simplement indéfini plutôt que ce n'est pas une propriété valide.</font></font>

<font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Si nous tapions pour</font></font> `myFlashObject`<font style="vertical-align: inherit;"><font style="vertical-align: inherit;">, nous aurions une belle erreur de compilation, comme indiqué ci-dessous:</font></font>

    type FlashObject = {
        name: string,
        age: string,
        location: string
    }

    public ngOnInit (): void {
        let myFlashObject: FlashObject = {
            name: 'My cool name',
            age: 'My cool age',
            // Compilation error
            Type '{ name: string; age: string; loc: string; }' is not assignable to type 'FlashObjectType'.
            Object literal may only specify known properties, and 'loc' does not exist in type 'FlashObjectType'.
            loc: 'My cool location'
        }
        this.processObject(myFlashObject);
    }

    public processObject(myObject: FlashObject): void {
        console.log(`Name: ${myObject.name}`);
        console.log(`Age: ${myObject.age}`)
        // Compilation error
        Property 'loc' does not exist on type 'FlashObjectType'.
        console.log(`Location: ${myObject.loc}`);
    }

<font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Si vous démarrez un nouveau projet, il est utile de définir</font></font> `strict:true`<font style="vertical-align: inherit;"><font style="vertical-align: inherit;">dans le</font></font> `tsconfig.json`<font style="vertical-align: inherit;"><font style="vertical-align: inherit;">fichier toutes les options de vérification de type strictes.</font></font>

#### <font style="vertical-align: inherit;"><font style="vertical-align: inherit;">11) Utiliser les règles de la charpie</font></font>

`[tslint](https://palantir.github.io/tslint/)`<font style="vertical-align: inherit;"><font style="vertical-align: inherit;">a différentes options intégrées dans déjà comme</font></font> `[no-any](https://palantir.github.io/tslint/rules/no-any)`<font style="vertical-align: inherit;"><font style="vertical-align: inherit;">,</font></font> `[no-magic-numbers](https://palantir.github.io/tslint/rules/no-magic-numbers)`<font style="vertical-align: inherit;"><font style="vertical-align: inherit;">,</font></font> `[no-console](https://palantir.github.io/tslint/rules/no-console)`<font style="vertical-align: inherit;"><font style="vertical-align: inherit;">, etc que vous pouvez configurer dans votre</font></font> `tslint.json`<font style="vertical-align: inherit;"><font style="vertical-align: inherit;">pour faire respecter certaines règles dans votre base de code.</font></font>

**<font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Pourquoi?</font></font>**

<font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Disposer de règles anti-peluches signifie que vous obtiendrez une belle erreur lorsque vous ferez quelque chose que vous ne devriez pas être.</font> <font style="vertical-align: inherit;">Cela assurera la cohérence de votre application et sa lisibilité.</font> <font style="vertical-align: inherit;">Veuillez vous référer</font></font> [<font style="vertical-align: inherit;"><font style="vertical-align: inherit;">ici</font></font>](https://palantir.github.io/tslint/rules/) <font style="vertical-align: inherit;"><font style="vertical-align: inherit;">pour plus de règles que vous pouvez configurer.</font></font>

<font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Certaines règles relatives à la charpie viennent même avec des corrections pour résoudre l’erreur de charpie.</font> <font style="vertical-align: inherit;">Si vous souhaitez configurer votre propre règle de charpie personnalisée, vous pouvez également le faire.</font> <font style="vertical-align: inherit;">Veuillez vous référer à</font></font> [<font style="vertical-align: inherit;"><font style="vertical-align: inherit;">cet article</font></font>](https://medium.com/@phenomnominal/custom-typescript-lint-rules-with-tsquery-and-tslint-144184b6ff2d) <font style="vertical-align: inherit;"><font style="vertical-align: inherit;">de</font></font> [<font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Craig Spence</font></font>](undefined) <font style="vertical-align: inherit;"><font style="vertical-align: inherit;">pour savoir comment écrire vos propres règles personnalisées à l'aide de</font></font> [<font style="vertical-align: inherit;"><font style="vertical-align: inherit;">TSQuery</font></font>](https://github.com/phenomnomnominal/tsquery) <font style="vertical-align: inherit;"><font style="vertical-align: inherit;">.</font></font>

**<font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Avant</font></font>**

    public ngOnInit (): void {
        console.log('I am a naughty console log message');
        console.warn('I am a naughty console warning message');
        console.error('I am a naughty console error message');
    }

    // Output
    No errors, prints the below on console window:
    I am a naughty console message
    I am a naughty console warning message
    I am a naughty console error message

**<font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Après</font></font>**

    // tslint.json
    {
        "rules": {
            .......
            "no-console": [
                 true,
                 "log",    // no console.log allowed
                 "warn"    // no console.warn allowed
            ]
       }
    }

    // ..component.ts

    public ngOnInit (): void {
        console.log('I am a naughty console log message');
        console.warn('I am a naughty console warning message');
        console.error('I am a naughty console error message');
    }

    // Output
    Lint errors for console.log and console.warn statements and no error for console.error as it is not mentioned in the config

    Calls to 'console.log' are not allowed.
    Calls to 'console.warn' are not allowed.

#### <font style="vertical-align: inherit;"><font style="vertical-align: inherit;">12) Petits composants réutilisables</font></font>

<font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Extrayez les pièces pouvant être réutilisées dans un composant et créez-en un nouveau.</font> <font style="vertical-align: inherit;">Faites le composant aussi bête que possible, car cela le fera fonctionner dans plus de scénarios.</font> <font style="vertical-align: inherit;">Rendre un composant muet signifie que le composant ne contient aucune logique particulière et fonctionne uniquement en fonction des entrées et des sorties qui lui sont fournies.</font></font>

<font style="vertical-align: inherit;"><font style="vertical-align: inherit;">En règle générale, le dernier enfant de l'arborescence des composants sera le plus stupide de tous.</font></font>

**<font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Pourquoi?</font></font>**

<font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Les composants réutilisables réduisent la duplication du code, facilitant ainsi la maintenance et les modifications.</font></font>

<font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Les composants muets sont plus simples, ils sont donc moins susceptibles d'avoir des bogues.</font> <font style="vertical-align: inherit;">Les composants stupides vous incitent à réfléchir plus sérieusement à l'API de composant public et vous aident à détecter les problèmes divers.</font></font>

#### <font style="vertical-align: inherit;"><font style="vertical-align: inherit;">13) Les composants ne doivent traiter que de la logique d'affichage</font></font>

<font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Évitez toute logique autre que la logique d'affichage dans votre composant et faites en sorte que le composant ne traite que de la logique d'affichage.</font></font>

**<font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Pourquoi?</font></font>**

<font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Les composants sont conçus à des fins de présentation et contrôlent ce que la vue doit faire.</font> <font style="vertical-align: inherit;">Toute logique métier doit être extraite dans ses propres méthodes / services, le cas échéant, en séparant la logique métier de la logique de vue.</font></font>

<font style="vertical-align: inherit;"><font style="vertical-align: inherit;">La logique métier est généralement plus facile à tester à l'unité lorsqu'elle est extraite vers un service et peut être réutilisée par tout autre composant nécessitant l'application de la même logique métier.</font></font>

#### <font style="vertical-align: inherit;"><font style="vertical-align: inherit;">14) Évitez les longues méthodes</font></font>

<font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Les méthodes longues indiquent généralement qu'ils font trop de choses.</font> <font style="vertical-align: inherit;">Essayez d'utiliser le principe de responsabilité unique.</font> <font style="vertical-align: inherit;">La méthode elle-même dans son ensemble pourrait faire une chose, mais à l'intérieur de celle-ci, il y a quelques autres opérations qui pourraient se produire.</font> <font style="vertical-align: inherit;">Nous pouvons extraire ces méthodes dans leur propre méthode et leur demander de faire une chose chacune et de les utiliser à la place.</font></font>

**<font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Pourquoi?</font></font>**

<font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Les méthodes longues sont difficiles à lire, à comprendre et à maintenir.</font> <font style="vertical-align: inherit;">Ils sont également sujets aux bugs, car changer une chose peut affecter beaucoup d'autres choses dans cette méthode.</font> <font style="vertical-align: inherit;">Ils rendent également difficile la refactorisation (qui est un élément clé dans toute application).</font></font>

<font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Ceci est parfois mesuré en tant que "</font></font> [<font style="vertical-align: inherit;"><font style="vertical-align: inherit;">complexité cyclomatique</font></font>](https://en.wikipedia.org/wiki/Cyclomatic_complexity) <font style="vertical-align: inherit;"><font style="vertical-align: inherit;">".</font> <font style="vertical-align: inherit;">Il existe également certaines</font></font> [<font style="vertical-align: inherit;"><font style="vertical-align: inherit;">règles TSLint</font></font>](https://www.npmjs.com/package/tslint-sonarts) <font style="vertical-align: inherit;"><font style="vertical-align: inherit;">pour détecter la complexité cyclomatique / cognitive, que vous pouvez utiliser dans votre projet pour éviter les bogues et détecter les odeurs de code et les problèmes de maintenabilité.</font></font>

#### <font style="vertical-align: inherit;"><font style="vertical-align: inherit;">15) SEC</font></font>

<font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Ne te répète pas.</font> <font style="vertical-align: inherit;">Assurez-vous de ne pas copier le même code à différents endroits de la base de code.</font> <font style="vertical-align: inherit;">Extrayez le code répété et utilisez-le à la place du code répété.</font></font>

**<font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Pourquoi?</font></font>**

<font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Avoir le même code à plusieurs endroits signifie que si nous voulons modifier la logique de ce code, nous devons le faire à plusieurs endroits.</font> <font style="vertical-align: inherit;">Cela le rend difficile à maintenir et est également sujet à des bogues pour lesquels nous pourrions manquer de le mettre à jour dans tous les cas.</font> <font style="vertical-align: inherit;">Il faut plus de temps pour apporter des modifications à la logique et le tester est également un processus long.</font></font>

<font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Dans ces cas, extrayez le code répétitif et utilisez-le à la place.</font> <font style="vertical-align: inherit;">Cela signifie qu'un seul endroit pour changer et une chose à tester.</font> <font style="vertical-align: inherit;">Avoir moins de code en double envoyé aux utilisateurs signifie que l'application sera plus rapide.</font></font>

#### <font style="vertical-align: inherit;"><font style="vertical-align: inherit;">16) Ajouter des mécanismes de cache</font></font>

<font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Lors des appels API, les réponses de certaines d’entre elles ne changent pas souvent.</font> <font style="vertical-align: inherit;">Dans ces cas, vous pouvez ajouter un mécanisme de mise en cache et stocker la valeur de l'API.</font> <font style="vertical-align: inherit;">Quand une autre demande est faite à la même API, vérifiez si elle a une valeur dans le cache et si oui, utilisez-la.</font> <font style="vertical-align: inherit;">Sinon, appelez l'API et mettez le résultat en cache.</font></font>

<font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Si les valeurs changent mais pas fréquemment, vous pouvez introduire une heure de cache afin de vérifier la date de la dernière mise en cache et décider d'appeler ou non l'API.</font></font>

**<font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Pourquoi?</font></font>**

<font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Avoir un mécanisme de cache signifie éviter les appels d'API indésirables.</font> <font style="vertical-align: inherit;">En effectuant uniquement les appels d'API lorsque cela est nécessaire et en évitant les doublons, la vitesse de l'application s'améliore, car nous n'avons pas à attendre le réseau.</font> <font style="vertical-align: inherit;">Cela signifie également que nous ne téléchargeons pas la même information, encore et encore.</font></font>

#### <font style="vertical-align: inherit;"><font style="vertical-align: inherit;">17) Évitez la logique dans les modèles</font></font>

<font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Si vos modèles contiennent une quelconque logique, même s’il s’agit d’une simple</font></font> `&&`<font style="vertical-align: inherit;"><font style="vertical-align: inherit;">clause, il est bon de l’extraire dans son composant.</font></font>

**<font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Pourquoi?</font></font>**

<font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Avoir une logique dans le modèle signifie qu'il n'est pas possible de le tester à l'unité et qu'il est donc plus sujet aux bugs lors de la modification du code du modèle.</font></font>

**<font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Avant</font></font>**

    // template
    <p *ngIf="role==='developer'"> Status: Developer </p>

    // component
    public ngOnInit (): void {
        this.role = 'developer';
    }

**<font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Après</font></font>**

    // template
    <p *ngIf="showDeveloperStatus"> Status: Developer </p>

    // component
    public ngOnInit (): void {
        this.role = 'developer';
        this.showDeveloperStatus = true;
    }

#### <font style="vertical-align: inherit;"><font style="vertical-align: inherit;">18) Les cordes doivent être en sécurité</font></font>

<font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Si vous avez une variable de type chaîne pouvant uniquement contenir un ensemble de valeurs, vous pouvez déclarer la liste des valeurs possibles au lieu de la déclarer en tant que type de chaîne.</font></font>

**<font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Pourquoi?</font></font>**

<font style="vertical-align: inherit;"><font style="vertical-align: inherit;">En déclarant le type de variable de manière appropriée, nous pouvons éviter les bogues lors de l'écriture du code pendant la compilation plutôt que pendant l'exécution.</font></font>

**<font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Avant</font></font>**

    private myStringValue: string;

    if (itShouldHaveFirstValue) {
       myStringValue = 'First';
    } else {
       myStringValue = 'Second'
    }

**<font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Après</font></font>**

    private myStringValue: 'First' | 'Second';

    if (itShouldHaveFirstValue) {
       myStringValue = 'First';
    } else {
       myStringValue = 'Other'
    }

    // This will give the below error
    Type '"Other"' is not assignable to type '"First" | "Second"'
    (property) AppComponent.myValue: "First" | "Second"

### **<font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Image plus grande</font></font>**

#### <font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Gestion d'état</font></font>

<font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Pensez à utiliser</font></font> [<font style="vertical-align: inherit;"><font style="vertical-align: inherit;">@ ngrx / store</font></font>](https://github.com/ngrx/platform) <font style="vertical-align: inherit;"><font style="vertical-align: inherit;">pour conserver l'état de votre application et à</font></font> [<font style="vertical-align: inherit;"><font style="vertical-align: inherit;">@ ngrx / effects</font></font>](https://github.com/ngrx/effects) <font style="vertical-align: inherit;"><font style="vertical-align: inherit;">comme modèle des effets secondaires pour le magasin.</font> <font style="vertical-align: inherit;">Les changements d'état sont décrits par les actions et les changements sont effectués par des fonctions pures, appelées réducteurs.</font></font>

**<font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Pourquoi?</font></font>**

_<font style="vertical-align: inherit;"><font style="vertical-align: inherit;">@ ngrx / store</font></font>_ <font style="vertical-align: inherit;"><font style="vertical-align: inherit;">isole toute la logique liée à l'état en un seul endroit et la rend cohérente dans toute l'application.</font> <font style="vertical-align: inherit;">Il a également mis en place un mécanisme de mémorisation lors de l’accès aux informations du magasin, ce qui conduit à une application plus performante.</font></font> _<font style="vertical-align: inherit;"><font style="vertical-align: inherit;">@ ngrx / store,</font></font>_ <font style="vertical-align: inherit;"><font style="vertical-align: inherit;">associé à la stratégie de détection des modifications d’Anngular, permet une application plus rapide.</font></font>

#### <font style="vertical-align: inherit;"><font style="vertical-align: inherit;">État immuable</font></font>

<font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Lorsque vous utilisez</font></font> _<font style="vertical-align: inherit;"><font style="vertical-align: inherit;">@ ngrx / store</font></font>_ <font style="vertical-align: inherit;"><font style="vertical-align: inherit;">, envisagez d’utiliser</font></font> [<font style="vertical-align: inherit;"><font style="vertical-align: inherit;">ngrx-store-freeze</font></font>](https://github.com/brandonroberts/ngrx-store-freeze) <font style="vertical-align: inherit;"><font style="vertical-align: inherit;">pour rendre l’état immuable.</font></font> _<font style="vertical-align: inherit;"><font style="vertical-align: inherit;">ngrx-store-freeze</font></font>_ <font style="vertical-align: inherit;"><font style="vertical-align: inherit;">empêche la mutation de l'état en lançant une exception.</font> <font style="vertical-align: inherit;">Cela évite une mutation accidentelle de l'état entraînant des conséquences indésirables.</font></font>

**<font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Pourquoi?</font></font>**

<font style="vertical-align: inherit;"><font style="vertical-align: inherit;">La mutation de l'état des composants conduit à un comportement incohérent de l'application en fonction de l'ordre de chargement des composants.</font> <font style="vertical-align: inherit;">Cela brise le modèle mental du modèle de redux.</font> <font style="vertical-align: inherit;">Les modifications peuvent être annulées si l’état du magasin est modifié et réémis.</font> <font style="vertical-align: inherit;">Séparation des problèmes - les composants sont des couches de vue, ils ne doivent pas savoir comment changer d'état.</font></font>

#### <font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Plaisanter</font></font>

[<font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Jest</font></font>](https://jestjs.io/) <font style="vertical-align: inherit;"><font style="vertical-align: inherit;">est le framework de tests unitaires de Facebook pour JavaScript.</font> <font style="vertical-align: inherit;">Il accélère les tests unitaires en parallélisant les tests sur la base de code.</font> <font style="vertical-align: inherit;">Avec son mode de surveillance, seuls les tests liés aux modifications apportées sont exécutés, ce qui raccourcit la boucle de rétroaction.</font> <font style="vertical-align: inherit;">Jest</font></font> <font style="vertical-align: inherit;"><font style="vertical-align: inherit;">fournit également une couverture de code des tests et est pris en charge sur VS Code et Webstorm.</font></font>

<font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Vous pouvez utiliser un</font> <font style="vertical-align: inherit;">paramètre</font></font> [<font style="vertical-align: inherit;"><font style="vertical-align: inherit;">prédéfini</font></font>](https://github.com/thymikee/jest-preset-angular) <font style="vertical-align: inherit;"><font style="vertical-align: inherit;">pour Jest qui fera le gros du travail lourd lors de la configuration de Jest dans votre projet.</font></font>

#### <font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Karma</font></font>

[<font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Karma</font></font>](https://karma-runner.github.io/2.0/index.html) <font style="vertical-align: inherit;"><font style="vertical-align: inherit;">est un coureur de test développé par l'équipe AngularJS.</font> <font style="vertical-align: inherit;">Il faut un vrai navigateur / DOM pour exécuter les tests.</font> <font style="vertical-align: inherit;">Il peut également fonctionner sur différents navigateurs.</font> <font style="vertical-align: inherit;">Jest n'a pas besoin de chrome headless / phantomjs pour exécuter les tests et s'exécute en pur nœud.</font></font>

#### <font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Universel</font></font>

<font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Si vous n'avez pas fait de votre application une application</font></font> _<font style="vertical-align: inherit;"><font style="vertical-align: inherit;">universelle</font></font>_ <font style="vertical-align: inherit;"><font style="vertical-align: inherit;">, le moment est venu de le faire.</font></font> [<font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Angular Universal</font></font>](https://angular.io/guide/universal) <font style="vertical-align: inherit;"><font style="vertical-align: inherit;">vous permet d’exécuter votre application Angular sur le serveur et effectue le rendu côté serveur (SSR), qui sert des pages HTML statiques pré-rendues.</font> <font style="vertical-align: inherit;">Cela rend l'application super rapide car elle affiche le contenu à l'écran presque instantanément, sans avoir à attendre le chargement et l'analyse des bundles JS, ni à démarrer Angular.</font></font>

<font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Il est également convivial pour le référencement, car Angular Universal génère un contenu statique et facilite l’indexation de l’application par les robots d'exploration de Web et la rend consultable sans exécuter JavaScript.</font></font>

**<font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Pourquoi?</font></font>**

<font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Universal améliore considérablement les performances de votre application.</font> <font style="vertical-align: inherit;">Nous avons récemment mis à jour notre application pour effectuer le rendu côté serveur et le temps de chargement du site est passé de quelques secondes à des dizaines de millisecondes !!</font></font>

<font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Cela permet également à votre site de s'afficher correctement dans les extraits de prévisualisation des médias sociaux.</font> <font style="vertical-align: inherit;">La première peinture significative est très rapide et rend le contenu visible pour les utilisateurs sans aucun retard indésirable.</font></font>

### <font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Conclusion</font></font>

<font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Construire des applications est un voyage constant et il y a toujours place à l'amélioration.</font> <font style="vertical-align: inherit;">Cette liste d’optimisations est un bon point de départ et l’application constante de ces modèles rendra votre équipe heureuse.</font> <font style="vertical-align: inherit;">Vos utilisateurs vous apprécieront également pour la bonne expérience de votre application moins boguée et performante.</font></font>
