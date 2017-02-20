node {
	stage 'Checkout and Setup'
		checkout scm
		sh 'cd fastlane'
	stage 'Lint'
		sh '/usr/local/bin/fastlane lint'
	stage 'Test'
		sh '/usr/local/bin/fastlane test'
	stage 'Build'
		def build_number = env.BUILD_NUMBER
		sh "/usr/local/bin/fastlane build build_number:${build_number}"
	stage 'Deploy'
		archive 'reports/, dist/'
		sh '/usr/local/bin/fastlane deploy'
}
