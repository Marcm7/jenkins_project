pipeline {
  agent any

  environment {
    VENV = 'venv'
    ACTIVATE = "source ${VENV}/bin/activate"
  }

  stages {
    stage('Setup') {
      steps {
        sh '''
          if [ ! -d "${VENV}" ]; then python3 -m venv ${VENV}; fi
          ${ACTIVATE} && pip install -U pip && pip install -r requirements.txt
        '''
      }
    }

    stage('Lint') {
      steps { sh '''${ACTIVATE} && flake8 app.py''' }
    }

    stage('Test') {
      steps { sh '''${ACTIVATE} && python -m pytest --maxfail=1 --disable-warnings -q''' }
    }

    // Graded additions
    stage('Coverage') {
      steps {
        sh '''
          ${ACTIVATE}
          coverage run -m pytest
          coverage report
        '''
      }
    }

    stage('Security Scan') {
      steps { sh '''${ACTIVATE} && bandit -q -r .''' }
    }

    stage('Deploy') {
      steps {
        echo "Deploying application locally..."
        sh '''${ACTIVATE} && python app.py'''
      }
    }
  }

  post { always { cleanWs() } }
}
