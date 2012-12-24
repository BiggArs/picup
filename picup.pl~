#!/usr/bin/env perl 

use strict;
use warnings;
use WWW::Mechanize;
use HTML::TreeBuilder;
use Getopt::Std;

my $usage = "Загрузка изображений на pic.dagtk.net 

Использование:
$0 [ключи] <файлы для загрузки>

Доступные ключи:
   	-t <строка> - тип выдаваемой ссылки (по умолчанию - original):
		original	- прямая ссылка на оригинал
		bbcode		- для форума
		html		- для сайта
		show		- для просмотра
   	-h - показать данное сообщение

Доступна загрузка jpeg, png, gif, tiff файлов размером до 10 мегабайт.
";

getopts('t:h');
our ( $opt_t, $opt_h );

die $usage if !@ARGV || $opt_h;

my @types = qw/html bbcode show original/;

if ( $opt_t ) {
	die "Недопустимый тип ссылки\nДопустимые значения: @types" unless grep { $_ eq $opt_t } @types;
}
else {
	$opt_t = qw/original/;
}

my @files = grep { 
	-e && -r && /(jpe?g|png|tiff?|bmp|gif)$/i 
	} @ARGV;

die "Не найдено файлов для загрузки\n" unless @files;

print @{ upload ( \@files ) };

sub upload {
	my $flist = shift;
	my ( @links, $link );

	my $mech = WWW::Mechanize->new(
		agent_alias		=> 'Linux Mozilla',
	);

	for my $img ( @$flist ) {
		print "Загрузка $img...\n";
		$mech->get( 'http://pic.dagtk.net' );
		$mech->submit_form(
			fields	=> {
				'upload' => $img,
			},
		);

		( print "Пропуск $img...\n" && next ) unless $mech->success;

		#parse content
		my $tr = HTML::TreeBuilder->new_from_content( $mech->content() );
		my @text = $tr->look_down( 
			type => 'text',
		);
	
		for my $tag ( @text ) {
			next unless $tag->attr( 'id' ) =~ qr/^$opt_t/i;
			$link = $tag->attr( 'value' ) . "\n";
			push @links, $link;
		};
	}
	
	die "Ошибка: не было загружено ни одного файла\n" unless @links;
	return \@links;
}

