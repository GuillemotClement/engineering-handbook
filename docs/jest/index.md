# Jest 

Permet de tester 

## Couche controller

On utilise `supertest` pour tester les endpoint et permettre l'envoie de requête

```ts
import { Test, TestingModule } from '@nestjs/testing';
import { AuthController } from './auth.controller';
import { AuthService } from './auth.service';
import { INestApplication } from '@nestjs/common';
import * as request from 'supertest';


// TEST A METTRE EN PLACE SUR LE CONTROLLEr
// que la route existe
// que la requête est bien reçue
// que les bons paramètres sont transmis au service
// que la réponse HTTP est correcte (status + body)
// que les erreurs sont bien transformées en codes HTTP

describe('AuthController', () => {
  // app est l'instance de l'application utiliser pour tester les routes HTTP
  // on créer cette instance avec le moduleRef.createNestApplication()
  // il permet à supertest d'envoyer les requête
  // app permet donc d'exposer le controller comme une api en mémoire pour les test => indispensable pour tester les routes
  let app: INestApplication;
  
  // permet de créer le mock du service. Pour la couche controller, on teste pas la couche service, uniquement le comportement du controller
  // de cette manière, on touche pas la DB ou le service réel
  // chaque méthode du service est remplacer par un mock avec jest.fn()
  // les test du controller sont isolés
  let authService = {
    login: jest.fn(), // ajout du mock. On indique uniquement que la fonction est mocker
    register: jest.fn(),
    logout: jest.fn(),
  }

  // avant chaque test, on viens charger les dépendances utilisé par le controller (ici authService)
  beforeEach(async () => {
    // on viens crér un module Nest minimal pour le test avec le controller et les service mocké
    const moduleRef: TestingModule = await Test.createTestingModule({
      // controller tester
      controllers: [AuthController],
      // service mocker
      providers: [{ provide: AuthService, useValue: authService }],
    }).compile();

    // transforme le module en application HTTP qui peut recevoir les requête via superTest
    app = moduleRef.createNestApplication();
    await app.init();
  });

  // après chaque test, on viens fermer l'aplication. 
  // permet d'éviter des fuites mémoire ou des ports bloqués si on définis plusieurs test
  afterAll(async () => {
    await app.close();
  });

  // TEST LOGIN ===============================
  describe('test /login', () => {
    it('should exist and return 200', async () => {
      // on créer un mock pour le résultat du login => on doit indiquer ce qui est cencé être répondu par le service
      // on viens définir explicitement les data retourner 
      authService.login.mockResolvedValue({ accessToken: 'token', user: { id: 1, email: 'test@mail.com'}});

      // on fait l'appel sur la route avec des test fake pour tester si le endpoint répond correctement
      return request(app.getHttpServer())
      // utile le chemin relatif, localhost:port est gérée en mémoire
        .post('/auth/login') // url complet http://localhost:3000/api/auth/login ?
        .send({ email: 'test@test.com', password: 12345678}) // on passe les donénées qui sont envoyé par le frontend
        .expect(200) // on vérifie le code HTTP de la réponse
        .expect((res) => {
          expect(res.body).toHaveProperty('accessToken'); // on check la structure de l'objet de réponse
          expect(res.body).toHaveProperty('user'); // on check qu'on as bien les donnée en reponse
          expect(res.body.user.email).toBe('test@test.com'); // on test qu'on as bien email retourner
        });
      });
    });
  });
  
})





// code mini => faudras rajouter le commenter dans ce code
import { Test, TestingModule } from '@nestjs/testing';
import { AuthController } from './auth.controller';
import { AuthService } from './auth.service';
import { INestApplication } from '@nestjs/common';
import request from 'supertest'; // import par défaut
import { Server } from 'http';

interface LoginResponse {
  accessToken: string;
  user: {
    id: number;
    email: string;
  };
}

describe('AuthController', () => {
  let app: INestApplication;

  const authService = {
    login: jest.fn(),
    register: jest.fn(),
    logout: jest.fn(),
  };

  beforeEach(async () => {
    const moduleRef: TestingModule = await Test.createTestingModule({
      controllers: [AuthController],
      providers: [{ provide: AuthService, useValue: authService }],
    }).compile();

    app = moduleRef.createNestApplication();
    await app.init();
  });

  afterAll(async () => {
    await app.close();
  });

  describe('POST /auth/login', () => {
    it('should exist and return 200', async () => {
      authService.login.mockResolvedValue({
        accessToken: 'token',
        user: { id: 1, email: 'test@test.com' },
      });

      return request(app.getHttpServer() as Server)
        .post('/auth/login')
        .send({ email: 'test@test.com', password: '12345678' })
        .expect(200)
        .expect((res: { body: LoginResponse }) => {
          expect(res.body).toHaveProperty('accessToken');
          expect(res.body).toHaveProperty('user');
          expect(res.body.user.email).toBe('test@test.com');
        });
    });
  });
});

```