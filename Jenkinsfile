#!groovy

node {
	checkout()
	build()
	push()
}

def checkout () {
	stage 'Checkout'
	deleteDir()
	checkout scm
}

def build () {
	sh "docker build -t sti-php:70-melissa ./7.0/"
}

def push () {
	sh "docker push sti-php-extra:70-melissa-new"
}